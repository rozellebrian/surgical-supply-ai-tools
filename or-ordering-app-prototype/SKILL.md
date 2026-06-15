
import React, { useState, useEffect, useCallback } from "react";
import {
  Plus, Trash2, Mail, Check, RefreshCw, Package,
  ClipboardList, Copy, Search, AlertTriangle, CheckCircle2, Clock
} from "lucide-react";

const STORAGE_KEY = "or-orders";
const BACKORDER_DAYS = 10;

const uid = () =>
  Date.now().toString(36) + Math.random().toString(36).slice(2, 7);

const fmtDate = (ts) =>
  new Date(ts).toLocaleDateString("en-US", {
    month: "short", day: "numeric", year: "2-digit",
  });

const daysSince = (ts) =>
  Math.floor((Date.now() - ts) / (1000 * 60 * 60 * 24));

export default function App() {
  const [role, setRole] = useState("staff"); // "staff" | "coordinator"
  const [orders, setOrders] = useState([]);
  const [loading, setLoading] = useState(true);
  const [syncNote, setSyncNote] = useState("");

  // ---- new order form state ----
  const [orderedBy, setOrderedBy] = useState("");
  const [rows, setRows] = useState([{ id: uid(), ref: "", qty: 1, note: "" }]);
  const [emailDraft, setEmailDraft] = useState(null);

  // ---- list controls ----
  const [search, setSearch] = useState("");
  const [statusFilter, setStatusFilter] = useState("all");
  const [copied, setCopied] = useState(false);

  const hasStorage = typeof window !== "undefined" && window.storage;

  const load = useCallback(async () => {
    setLoading(true);
    if (!hasStorage) {
      setSyncNote("Local session only — storage not available in this view.");
      setLoading(false);
      return;
    }
    try {
      const res = await window.storage.get(STORAGE_KEY, true);
      const list = res && res.value ? JSON.parse(res.value) : [];
      setOrders(Array.isArray(list) ? list : []);
      setSyncNote("");
    } catch {
      setOrders([]);
    }
    setLoading(false);
  }, [hasStorage]);

  useEffect(() => { load(); }, [load]);

  const persist = useCallback(async (next) => {
    setOrders(next);
    if (!hasStorage) return;
    try {
      await window.storage.set(STORAGE_KEY, JSON.stringify(next), true);
    } catch {
      setSyncNote("Save failed — try Refresh and re-enter.");
    }
  }, [hasStorage]);

  // ---- form handlers ----
  const addRow = () =>
    setRows((r) => [...r, { id: uid(), ref: "", qty: 1, note: "" }]);
  const removeRow = (id) =>
    setRows((r) => (r.length === 1 ? r : r.filter((x) => x.id !== id)));
  const updateRow = (id, key, val) =>
    setRows((r) => r.map((x) => (x.id === id ? { ...x, [key]: val } : x)));

  const validRows = rows.filter((r) => r.ref.trim() !== "");

  const placeOrder = async () => {
    if (!orderedBy.trim() || validRows.length === 0) return;
    const batchId = uid();
    const now = Date.now();
    const records = validRows.map((r) => ({
      id: uid(),
      batchId,
      orderedBy: orderedBy.trim(),
      ref: r.ref.trim(),
      qty: Number(r.qty) || 1,
      staffNote: r.note.trim(),
      coordNote: "",
      ordered: false,
      orderedAt: null,
      received: false,
      receivedAt: null,
      createdAt: now,
    }));
    await persist([...records, ...orders]);
    buildEmail(orderedBy.trim(), records, now);
    setRows([{ id: uid(), ref: "", qty: 1, note: "" }]);
  };

  const buildEmail = (who, records, ts) => {
    const lines = records.map(
      (r) =>
        `  • REF ${r.ref}  |  QTY ${r.qty}` +
        (r.staffNote ? `  |  ${r.staffNote}` : "")
    );
    const body =
      `New OR supply order placed.\n\n` +
      `Ordered by: ${who}\n` +
      `Date: ${new Date(ts).toLocaleString()}\n\n` +
      `Items:\n${lines.join("\n")}\n\n` +
      `— Logged in OR Supply Orders`;
    setEmailDraft({
      subject: `OR Supply Order — ${who} — ${fmtDate(ts)}`,
      body,
    });
  };

  const copyEmail = async () => {
    if (!emailDraft) return;
    const text = `Subject: ${emailDraft.subject}\n\n${emailDraft.body}`;
    try {
      await navigator.clipboard.writeText(text);
      setCopied(true);
      setTimeout(() => setCopied(false), 1800);
    } catch {/* clipboard blocked */}
  };

  // ---- coordinator actions ----
  const toggleOrdered = (id) =>
    persist(
      orders.map((o) =>
        o.id === id
          ? {
              ...o,
              ordered: !o.ordered,
              orderedAt: !o.ordered ? Date.now() : null,
            }
          : o
      )
    );

  const toggleReceived = (id) =>
    persist(
      orders.map((o) =>
        o.id === id
          ? {
              ...o,
              received: !o.received,
              receivedAt: !o.received ? Date.now() : null,
              ordered: !o.received ? true : o.ordered,
              orderedAt: !o.received && !o.orderedAt ? Date.now() : o.orderedAt,
            }
          : o
      )
    );

  const setCoordNote = (id, val) =>
    persist(orders.map((o) => (o.id === id ? { ...o, coordNote: val } : o)));

  const removeRecord = (id) =>
    persist(orders.filter((o) => o.id !== id));

  // ---- derived ----
  const statusOf = (o) => {
    if (o.received) return "received";
    if (o.ordered) return "ordered";
    return "awaiting";
  };

  const filtered = orders.filter((o) => {
    const s = statusOf(o);
    if (statusFilter !== "all" && s !== statusFilter) return false;
    if (statusFilter === "all" && false) return false;
    const q = search.trim().toLowerCase();
    if (!q) return true;
    return (
      o.ref.toLowerCase().includes(q) ||
      o.orderedBy.toLowerCase().includes(q) ||
      (o.staffNote || "").toLowerCase().includes(q)
    );
  });

  const counts = {
    awaiting: orders.filter((o) => statusOf(o) === "awaiting").length,
    ordered: orders.filter((o) => statusOf(o) === "ordered").length,
    received: orders.filter((o) => statusOf(o) === "received").length,
  };

  const isCoord = role === "coordinator";

  return (
    <div className="min-h-screen bg-slate-50 text-slate-900">
      {/* Header */}
      <header className="bg-teal-800 text-white">
        <div className="max-w-6xl mx-auto px-4 sm:px-6 py-4 flex items-center justify-between gap-4 flex-wrap">
          <div className="flex items-center gap-3">
            <div className="bg-teal-700 rounded-lg p-2">
              <Package className="w-5 h-5" />
            </div>
            <div>
              <h1 className="text-lg font-semibold tracking-tight leading-none">
                OR Supply Orders
              </h1>
              <p className="text-teal-200 text-xs mt-1">
                Shady Grove — Surgical Supply
              </p>
            </div>
          </div>

          <div className="flex items-center gap-1 bg-teal-900/40 rounded-lg p-1">
            {["staff", "coordinator"].map((r) => (
              <button
                key={r}
                onClick={() => setRole(r)}
                className={`px-3 py-1.5 rounded-md text-sm font-medium transition ${
                  role === r
                    ? "bg-white text-teal-800"
                    : "text-teal-100 hover:bg-teal-700/50"
                }`}
              >
                {r === "staff" ? "Coordinator" : "Brian"}
              </button>
            ))}
          </div>
        </div>
      </header>

      <main className="max-w-6xl mx-auto px-4 sm:px-6 py-6 grid grid-cols-1 lg:grid-cols-12 gap-6">
        {/* New order */}
        <section className="lg:col-span-5 xl:col-span-4">
          <div className="bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden">
            <div className="px-5 py-3 border-b border-slate-100 flex items-center gap-2">
              <ClipboardList className="w-4 h-4 text-teal-700" />
              <h2 className="font-semibold text-sm">New order</h2>
            </div>

            <div className="p-5 space-y-4">
              <div>
                <label className="block text-xs font-medium text-slate-500 mb-1">
                  Ordered by
                </label>
                <input
                  value={orderedBy}
                  onChange={(e) => setOrderedBy(e.target.value)}
                  placeholder="Your name"
                  className="w-full rounded-lg border border-slate-300 px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-teal-500 focus:border-teal-500"
                />
              </div>

              <div className="space-y-3">
                <div className="flex items-center justify-between">
                  <label className="text-xs font-medium text-slate-500">
                    Items ({validRows.length})
                  </label>
                </div>

                {rows.map((r, i) => (
                  <div
                    key={r.id}
                    className="rounded-lg border border-slate-200 bg-slate-50/60 p-3 space-y-2"
                  >
                    <div className="flex items-center justify-between">
                      <span className="text-[11px] font-semibold text-slate-400 uppercase tracking-wide">
                        Line {i + 1}
                      </span>
                      <button
                        onClick={() => removeRow(r.id)}
                        disabled={rows.length === 1}
                        className="text-slate-400 hover:text-rose-500 disabled:opacity-30 disabled:hover:text-slate-400"
                        aria-label="Remove line"
                      >
                        <Trash2 className="w-4 h-4" />
                      </button>
                    </div>
                    <div className="flex gap-2">
                      <input
                        value={r.ref}
                        onChange={(e) => updateRow(r.id, "ref", e.target.value)}
                        placeholder="Reference #"
                        className="flex-1 rounded-md border border-slate-300 px-2.5 py-1.5 text-sm font-mono focus:outline-none focus:ring-2 focus:ring-teal-500 focus:border-teal-500"
                      />
                      <input
                        type="number"
                        min="1"
                        value={r.qty}
                        onChange={(e) => updateRow(r.id, "qty", e.target.value)}
                        className="w-16 rounded-md border border-slate-300 px-2 py-1.5 text-sm text-center focus:outline-none focus:ring-2 focus:ring-teal-500 focus:border-teal-500"
                      />
                    </div>
                    <input
                      value={r.note}
                      onChange={(e) => updateRow(r.id, "note", e.target.value)}
                      placeholder="Comments (optional)"
                      className="w-full rounded-md border border-slate-300 px-2.5 py-1.5 text-sm focus:outline-none focus:ring-2 focus:ring-teal-500 focus:border-teal-500"
                    />
                  </div>
                ))}

                <button
                  onClick={addRow}
                  className="w-full flex items-center justify-center gap-1.5 rounded-lg border border-dashed border-slate-300 py-2 text-sm text-slate-600 hover:border-teal-400 hover:text-teal-700 transition"
                >
                  <Plus className="w-4 h-4" /> Add line
                </button>
              </div>

              <button
                onClick={placeOrder}
                disabled={!orderedBy.trim() || validRows.length === 0}
                className="w-full rounded-lg bg-teal-700 text-white py-2.5 text-sm font-semibold hover:bg-teal-800 disabled:opacity-40 disabled:cursor-not-allowed transition"
              >
                Place order
              </button>
            </div>
          </div>

          {/* Email draft */}
          {emailDraft && (
            <div className="mt-4 bg-white rounded-xl border border-teal-200 shadow-sm overflow-hidden">
              <div className="px-5 py-3 border-b border-slate-100 flex items-center justify-between">
                <div className="flex items-center gap-2">
                  <Mail className="w-4 h-4 text-teal-700" />
                  <h2 className="font-semibold text-sm">Email draft</h2>
                </div>
                <button
                  onClick={() => setEmailDraft(null)}
                  className="text-xs text-slate-400 hover:text-slate-600"
                >
                  Dismiss
                </button>
              </div>
              <div className="p-5 space-y-3">
                <div className="text-xs">
                  <span className="text-slate-400">Subject: </span>
                  <span className="font-medium">{emailDraft.subject}</span>
                </div>
                <pre className="text-xs bg-slate-50 border border-slate-200 rounded-lg p-3 whitespace-pre-wrap font-sans text-slate-700">
{emailDraft.body}
                </pre>
                <button
                  onClick={copyEmail}
                  className="w-full flex items-center justify-center gap-1.5 rounded-lg bg-slate-900 text-white py-2 text-sm font-medium hover:bg-slate-800 transition"
                >
                  {copied ? <Check className="w-4 h-4" /> : <Copy className="w-4 h-4" />}
                  {copied ? "Copied" : "Copy email"}
                </button>
              </div>
            </div>
          )}
        </section>

        {/* Tracking */}
        <section className="lg:col-span-7 xl:col-span-8">
          {/* status bar */}
          <div className="grid grid-cols-3 gap-3 mb-4">
            <StatTile label="Awaiting order" value={counts.awaiting} tone="slate" />
            <StatTile label="Ordered" value={counts.ordered} tone="amber" />
            <StatTile label="Received" value={counts.received} tone="emerald" />
          </div>

          <div className="bg-white rounded-xl border border-slate-200 shadow-sm">
            <div className="px-4 sm:px-5 py-3 border-b border-slate-100 flex items-center gap-3 flex-wrap">
              <div className="relative flex-1 min-w-[160px]">
                <Search className="w-4 h-4 text-slate-400 absolute left-2.5 top-1/2 -translate-y-1/2" />
                <input
                  value={search}
                  onChange={(e) => setSearch(e.target.value)}
                  placeholder="Search ref #, name, note"
                  className="w-full rounded-lg border border-slate-300 pl-8 pr-3 py-1.5 text-sm focus:outline-none focus:ring-2 focus:ring-teal-500 focus:border-teal-500"
                />
              </div>
              <select
                value={statusFilter}
                onChange={(e) => setStatusFilter(e.target.value)}
                className="rounded-lg border border-slate-300 px-2.5 py-1.5 text-sm focus:outline-none focus:ring-2 focus:ring-teal-500"
              >
                <option value="all">All</option>
                <option value="awaiting">Awaiting order</option>
                <option value="ordered">Ordered</option>
                <option value="received">Received</option>
              </select>
              <button
                onClick={load}
                className="flex items-center gap-1.5 rounded-lg border border-slate-300 px-2.5 py-1.5 text-sm text-slate-600 hover:bg-slate-50"
              >
                <RefreshCw className={`w-4 h-4 ${loading ? "animate-spin" : ""}`} />
                Refresh
              </button>
            </div>

            {syncNote && (
              <div className="px-5 py-2 bg-amber-50 text-amber-800 text-xs border-b border-amber-100">
                {syncNote}
              </div>
            )}

            <div className="divide-y divide-slate-100">
              {loading ? (
                <div className="p-10 text-center text-slate-400 text-sm">Loading…</div>
              ) : filtered.length === 0 ? (
                <div className="p-10 text-center">
                  <Package className="w-8 h-8 text-slate-300 mx-auto mb-2" />
                  <p className="text-slate-500 text-sm">
                    {orders.length === 0
                      ? "No orders yet. Add one on the left."
                      : "Nothing matches that filter."}
                  </p>
                </div>
              ) : (
                filtered.map((o) => (
                  <OrderRow
                    key={o.id}
                    o={o}
                    status={statusOf(o)}
                    isCoord={isCoord}
                    onOrdered={() => toggleOrdered(o.id)}
                    onReceived={() => toggleReceived(o.id)}
                    onNote={(v) => setCoordNote(o.id, v)}
                    onDelete={() => removeRecord(o.id)}
                  />
                ))
              )}
            </div>
          </div>
          <p className="text-[11px] text-slate-400 mt-3 px-1">
            Shared list — everyone with this app sees the same orders. Only Brian can mark Ordered / Received and add notes.
          </p>
        </section>
      </main>
    </div>
  );
}

function StatTile({ label, value, tone }) {
  const tones = {
    slate: "border-slate-200 text-slate-700",
    amber: "border-amber-200 text-amber-700",
    emerald: "border-emerald-200 text-emerald-700",
  };
  return (
    <div className={`bg-white rounded-xl border ${tones[tone]} px-4 py-3`}>
      <div className="text-2xl font-semibold tracking-tight">{value}</div>
      <div className="text-[11px] text-slate-500 mt-0.5">{label}</div>
    </div>
  );
}

function OrderRow({ o, status, isCoord, onOrdered, onReceived, onNote, onDelete }) {
  const [draft, setDraft] = useState(o.coordNote || "");
  useEffect(() => setDraft(o.coordNote || ""), [o.coordNote]);

  const aging = o.ordered && !o.received && o.orderedAt;
  const ageDays = aging ? daysSince(o.orderedAt) : 0;
  const backordered = aging && ageDays >= BACKORDER_DAYS;

  const bar =
    status === "received"
      ? "bg-emerald-500"
      : status === "ordered"
      ? backordered ? "bg-rose-500" : "bg-amber-500"
      : "bg-slate-300";

  return (
    <div className="flex">
      <div className={`w-1 shrink-0 ${bar}`} />
      <div className="flex-1 p-4 sm:px-5">
        <div className="flex items-start justify-between gap-3 flex-wrap">
          <div className="min-w-0">
            <div className="flex items-center gap-2 flex-wrap">
              <span className="font-mono text-sm font-semibold">{o.ref}</span>
              <span className="text-xs bg-slate-100 text-slate-600 rounded px-1.5 py-0.5">
                ×{o.qty}
              </span>
              <StatusPill status={status} backordered={backordered} ageDays={ageDays} />
            </div>
            <div className="text-xs text-slate-500 mt-1">
              {o.orderedBy} · {fmtDate(o.createdAt)}
            </div>
            {o.staffNote && (
              <div className="text-sm text-slate-700 mt-1.5">{o.staffNote}</div>
            )}
          </div>

          <div className="flex items-center gap-2">
            <CheckToggle
              label="Ordered"
              on={o.ordered}
              disabled={!isCoord}
              onClick={onOrdered}
              tone="amber"
            />
            <CheckToggle
              label="Received"
              on={o.received}
              disabled={!isCoord}
              onClick={onReceived}
              tone="emerald"
            />
            {isCoord && (
              <button
                onClick={onDelete}
                className="text-slate-300 hover:text-rose-500 p-1"
                aria-label="Delete"
              >
                <Trash2 className="w-4 h-4" />
              </button>
            )}
          </div>
        </div>

        {/* coordinator note */}
        <div className="mt-2">
          {isCoord ? (
            <input
              value={draft}
              onChange={(e) => setDraft(e.target.value)}
              onBlur={() => draft !== o.coordNote && onNote(draft)}
              placeholder="Add a note for staff…"
              className="w-full rounded-md border border-slate-200 bg-teal-50/40 px-2.5 py-1.5 text-sm focus:outline-none focus:ring-2 focus:ring-teal-500 focus:border-teal-500"
            />
          ) : (
            o.coordNote && (
              <div className="text-sm rounded-md bg-teal-50 border border-teal-100 text-teal-900 px-2.5 py-1.5">
                <span className="text-[11px] font-semibold text-teal-600 uppercase tracking-wide mr-1">
                  Brian:
                </span>
                {o.coordNote}
              </div>
            )
          )}
        </div>
      </div>
    </div>
  );
}

function StatusPill({ status, backordered, ageDays }) {
  if (status === "received")
    return (
      <span className="inline-flex items-center gap-1 text-[11px] font-medium text-emerald-700 bg-emerald-50 rounded-full px-2 py-0.5">
        <CheckCircle2 className="w-3 h-3" /> Received
      </span>
    );
  if (status === "ordered")
    return backordered ? (
      <span className="inline-flex items-center gap-1 text-[11px] font-medium text-rose-700 bg-rose-50 rounded-full px-2 py-0.5">
        <AlertTriangle className="w-3 h-3" /> Back-order? {ageDays}d
      </span>
    ) : (
      <span className="inline-flex items-center gap-1 text-[11px] font-medium text-amber-700 bg-amber-50 rounded-full px-2 py-0.5">
        <Clock className="w-3 h-3" /> Ordered {ageDays ? `${ageDays}d` : ""}
      </span>
    );
  return (
    <span className="text-[11px] font-medium text-slate-500 bg-slate-100 rounded-full px-2 py-0.5">
      Awaiting order
    </span>
  );
}

function CheckToggle({ label, on, disabled, onClick, tone }) {
  const tones = {
    amber: on ? "bg-amber-500 border-amber-500 text-white" : "",
    emerald: on ? "bg-emerald-600 border-emerald-600 text-white" : "",
  };
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      className={`flex items-center gap-1 rounded-md border px-2 py-1 text-xs font-medium transition
        ${on ? tones[tone] : "border-slate-300 text-slate-500 bg-white"}
        ${disabled ? "opacity-50 cursor-default" : "hover:border-slate-400"}`}
    >
      <span
        className={`w-3.5 h-3.5 rounded-sm border flex items-center justify-center
          ${on ? "border-white/70" : "border-slate-400"}`}
      >
        {on && <Check className="w-2.5 h-2.5" />}
      </span>
      {label}
    </button>
  );
}
