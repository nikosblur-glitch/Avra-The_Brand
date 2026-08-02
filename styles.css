/* Avra The Brand */
const DEFAULT_PRODUCTS = [
  { id: 1, name: "Mini Bag Classic", desc: "Μικρή crochet τσάντα για καθημερινή χρήση.", price: 45, badge: "Νέο", photo: null },
  { id: 2, name: "Tote Soft", desc: "Ευρύχωρη tote με μαλακό νήμα.", price: 68, badge: null, photo: null },
  { id: 3, name: "Crossbody Line", desc: "Πρακτική crossbody με ρυθμιζόμενο λουράκι.", price: 55, badge: null, photo: null },
  { id: 4, name: "Clutch Evening", desc: "Κομψό clutch για βραδινές εμφανίσεις.", price: 38, badge: "Best", photo: null },
  { id: 5, name: "Shoulder Bag", desc: "Τσάντα ώμου με καθαρό σχέδιο.", price: 72, badge: null, photo: null },
  { id: 6, name: "Bucket Crochet", desc: "Bucket style με σταθερή βάση.", price: 62, badge: "Νέο", photo: null }
];

let products = JSON.parse(localStorage.getItem("avra_products") || "null") || DEFAULT_PRODUCTS;
let cart = JSON.parse(localStorage.getItem("avra_cart") || "[]");
let user = JSON.parse(localStorage.getItem("avra_user") || "null");
let texts = JSON.parse(localStorage.getItem("avra_texts") || "{}");
let pendingPhoto = null;

const $ = (s) => document.querySelector(s);
const $$ = (s) => [...document.querySelectorAll(s)];

document.addEventListener("DOMContentLoaded", () => {
  renderProducts();
  updateCartUI();
  updateAuthUI();
  applyTexts();
  updateStudioVisibility();
  setupScroll();
  setupHeader();
  setupEvents();
});

function renderProducts() {
  const g = $("#products-grid");
  if (!g) return;
  g.innerHTML = products.map((p) => `
    <article class="product-card aos">
      <div class="product-img">
        ${p.badge ? `<span class="product-badge">${p.badge}</span>` : ""}
        ${p.photo
          ? `<img src="${p.photo}" alt="${p.name}" />`
          : `<span class="emoji-fallback">🧶</span>`}
      </div>
      <div class="product-info">
        <h3 class="product-name">${p.name}</h3>
        <p class="product-desc">${p.desc}</p>
        <div class="product-foot">
          <span class="product-price">${p.price}€ <small>EUR</small></span>
          <button class="add-cart" data-id="${p.id}">+ Καλάθι</button>
        </div>
      </div>
    </article>
  `).join("");
  // re-observe aos
  $$(".aos").forEach((el) => {
    if (!el.classList.contains("visible")) {
      const obs = new IntersectionObserver((entries) => {
        entries.forEach((e) => { if (e.isIntersecting) e.target.classList.add("visible"); });
      }, { threshold: 0.1 });
      obs.observe(el);
    }
  });
}

function saveProducts() {
  localStorage.setItem("avra_products", JSON.stringify(products));
  renderProducts();
}

function saveCart() {
  localStorage.setItem("avra_cart", JSON.stringify(cart));
  updateCartUI();
}

function addToCart(id) {
  const p = products.find((x) => x.id === id);
  if (!p) return;
  const e = cart.find((i) => i.id === id);
  if (e) e.qty++;
  else cart.push({ id, name: p.name, price: p.price, photo: p.photo, qty: 1 });
  saveCart();
  showToast("Προστέθηκε: " + p.name);
  const btn = $(`.add-cart[data-id="${id}"]`);
  if (btn) {
    btn.classList.add("added");
    btn.textContent = "✓";
    setTimeout(() => { btn.classList.remove("added"); btn.textContent = "+ Καλάθι"; }, 1200);
  }
}

function removeFromCart(id) {
  cart = cart.filter((i) => i.id !== id);
  saveCart();
  renderCartItems();
}

function changeQty(id, d) {
  const i = cart.find((x) => x.id === id);
  if (!i) return;
  i.qty += d;
  if (i.qty <= 0) removeFromCart(id);
  else { saveCart(); renderCartItems(); }
}

function cartTotal() {
  return cart.reduce((s, i) => s + i.price * i.qty, 0);
}

function updateCartUI() {
  const n = cart.reduce((s, i) => s + i.qty, 0);
  const b = $("#cart-count");
  if (b) {
    b.textContent = n;
    b.classList.toggle("show", n > 0);
  }
  renderCartItems();
}

function renderCartItems() {
  const c = $("#cart-items");
  const t = $("#cart-total-val");
  const ck = $("#checkout-btn");
  if (!c) return;
  if (!cart.length) {
    c.innerHTML = `<div class="cart-empty"><p>Το καλάθι είναι άδειο</p></div>`;
    if (t) t.textContent = "0€";
    if (ck) ck.disabled = true;
    return;
  }
  c.innerHTML = cart.map((i) => `
    <div class="cart-item">
      <div class="cart-item-img">
        ${i.photo ? `<img src="${i.photo}" alt="" />` : `<span class="emoji-fallback">🧶</span>`}
      </div>
      <div>
        <div class="cart-item-name">${i.name}</div>
        <div class="cart-item-price">${i.price}€</div>
        <div class="cart-item-qty">
          <button class="qty-btn" data-a="dec" data-id="${i.id}">−</button>
          <span>${i.qty}</span>
          <button class="qty-btn" data-a="inc" data-id="${i.id}">+</button>
        </div>
        <button class="cart-item-rm" data-id="${i.id}">Αφαίρεση</button>
      </div>
    </div>
  `).join("");
  if (t) t.textContent = cartTotal() + "€";
  if (ck) ck.disabled = false;
}

function openCart() {
  $("#cart-ov").classList.add("open");
  $("#cart-dr").classList.add("open");
  document.body.style.overflow = "hidden";
}
function closeCart() {
  $("#cart-ov").classList.remove("open");
  $("#cart-dr").classList.remove("open");
  document.body.style.overflow = "";
}

function openAuth() {
  $("#auth-modal").classList.add("open");
  document.body.style.overflow = "hidden";
  updateAuthBody();
}
function closeAuth() {
  $("#auth-modal").classList.remove("open");
  document.body.style.overflow = "";
}

function updateAuthBody() {
  const b = $("#auth-body");
  if (!b) return;
  if (user) {
    b.innerHTML = `
      <div class="user-info">
        <div class="user-av">${user.name[0]}</div>
        <div>
          <div class="user-n">${user.name}</div>
          <div class="user-e">${user.email}</div>
        </div>
      </div>
      <p style="text-align:center;color:var(--muted);font-size:.85rem;margin-bottom:.7rem">Συνδεδεμένος/η — το Studio είναι ανοιχτό</p>
      <button class="logout-btn" id="logout-btn">Αποσύνδεση</button>`;
    $("#logout-btn").onclick = logout;
  } else {
    b.innerHTML = `
      <button class="google-btn" id="g-login">
        <svg viewBox="0 0 24 24">
          <path fill="#4285F4" d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z"/>
          <path fill="#34A853" d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z"/>
          <path fill="#FBBC05" d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z"/>
          <path fill="#EA4335" d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z"/>
        </svg>
        Σύνδεση με Google
      </button>
      <div class="auth-div">demo</div>
      <p style="text-align:center;font-size:.78rem;color:var(--muted)">Η σύνδεση είναι προσομοίωση για το static site.</p>`;
    $("#g-login").onclick = mockLogin;
  }
}

function mockLogin() {
  user = { name: "Admin Avra", email: "admin@avrathebrand.gr" };
  localStorage.setItem("avra_user", JSON.stringify(user));
  updateAuthUI();
  updateAuthBody();
  updateStudioVisibility();
  showToast("Καλωσήρθες — το Studio άνοιξε");
  setTimeout(closeAuth, 700);
}

function logout() {
  user = null;
  localStorage.removeItem("avra_user");
  updateAuthUI();
  updateAuthBody();
  updateStudioVisibility();
  showToast("Αποσυνδέθηκες");
  setTimeout(closeAuth, 500);
}

function updateAuthUI() {
  const btn = $("#user-btn");
  if (!btn) return;
  if (user) {
    btn.innerHTML = `<span style="font-weight:700;font-size:.9rem">${user.name[0]}</span>`;
    btn.title = user.name;
  } else {
    btn.innerHTML = `<svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M20 21v-2a4 4 0 00-4-4H8a4 4 0 00-4 4v2"/><circle cx="12" cy="7" r="4"/></svg>`;
    btn.title = "Σύνδεση με Google";
  }
}

function updateStudioVisibility() {
  const studio = $("#studio");
  const nav = $("#nav-studio");
  const mob = $("#mobile-studio");
  const show = !!user;
  if (studio) studio.hidden = !show;
  if (nav) nav.hidden = !show;
  if (mob) mob.hidden = !show;
  if (show && texts) {
    if ($("#t-hero-title")) $("#t-hero-title").value = texts.heroTitle || "";
    if ($("#t-hero-desc")) $("#t-hero-desc").value = texts.heroDesc || "";
    if ($("#t-about")) $("#t-about").value = texts.about || "";
  }
}

function applyTexts() {
  if (texts.heroTitle) {
    const el = $("#hero-title");
    if (el) el.innerHTML = texts.heroTitle;
  }
  if (texts.heroDesc) {
    const el = $("#hero-desc");
    if (el) el.textContent = texts.heroDesc;
  }
  if (texts.about) {
    const el = $("#about-text");
    if (el) el.textContent = texts.about;
  }
}

function showToast(msg) {
  let t = $("#toast");
  if (!t) {
    t = document.createElement("div");
    t.id = "toast";
    t.className = "toast";
    document.body.appendChild(t);
  }
  t.textContent = msg;
  t.classList.add("show");
  clearTimeout(t._tm);
  t._tm = setTimeout(() => t.classList.remove("show"), 2500);
}

function setupScroll() {
  const obs = new IntersectionObserver((entries) => {
    entries.forEach((e) => { if (e.isIntersecting) e.target.classList.add("visible"); });
  }, { threshold: 0.1 });
  $$(".aos").forEach((el) => obs.observe(el));
}

function setupHeader() {
  window.addEventListener("scroll", () => {
    $(".header")?.classList.toggle("scrolled", window.scrollY > 30);
  }, { passive: true });
}

function setupEvents() {
  document.addEventListener("click", (e) => {
    const a = e.target.closest(".add-cart");
    if (a) { addToCart(+a.dataset.id); return; }
    const q = e.target.closest(".qty-btn");
    if (q) { changeQty(+q.dataset.id, q.dataset.a === "inc" ? 1 : -1); return; }
    const r = e.target.closest(".cart-item-rm");
    if (r) { removeFromCart(+r.dataset.id); return; }
  });

  $("#cart-btn")?.addEventListener("click", openCart);
  $("#cart-x")?.addEventListener("click", closeCart);
  $("#cart-ov")?.addEventListener("click", closeCart);

  $("#user-btn")?.addEventListener("click", openAuth);
  $("#auth-x")?.addEventListener("click", closeAuth);
  $("#auth-modal")?.addEventListener("click", (e) => {
    if (e.target === $("#auth-modal")) closeAuth();
  });

  $("#checkout-btn")?.addEventListener("click", () => {
    if (!user) {
      closeCart();
      openAuth();
      showToast("Συνδέσου πρώτα");
      return;
    }
    showToast("Ευχαριστούμε! (Demo)");
    cart = [];
    saveCart();
    closeCart();
  });

  $("#mobile-toggle")?.addEventListener("click", () => $("#mobile-menu")?.classList.add("open"));
  $("#mobile-x")?.addEventListener("click", () => $("#mobile-menu")?.classList.remove("open"));
  $$("#mobile-menu a").forEach((a) => {
    a.addEventListener("click", () => $("#mobile-menu")?.classList.remove("open"));
  });

  // Photo preview
  $("#p-photo")?.addEventListener("change", (e) => {
    const file = e.target.files?.[0];
    pendingPhoto = null;
    const prev = $("#photo-preview");
    if (!file || !prev) return;
    const reader = new FileReader();
    reader.onload = () => {
      pendingPhoto = reader.result;
      prev.innerHTML = `<img src="${pendingPhoto}" alt="preview" />`;
    };
    reader.readAsDataURL(file);
  });

  // Add product
  $("#product-form")?.addEventListener("submit", (e) => {
    e.preventDefault();
    if (!user) return;
    const name = $("#p-name").value.trim();
    const desc = $("#p-desc").value.trim();
    const price = +$("#p-price").value;
    if (!name || !desc || !price) return;
    const id = Date.now();
    products.unshift({
      id,
      name,
      desc,
      price,
      badge: "Νέο",
      photo: pendingPhoto
    });
    saveProducts();
    showToast("Η τσάντα προστέθηκε");
    e.target.reset();
    pendingPhoto = null;
    if ($("#photo-preview")) $("#photo-preview").innerHTML = "";
  });

  // Texts
  $("#texts-form")?.addEventListener("submit", (e) => {
    e.preventDefault();
    if (!user) return;
    texts = {
      heroTitle: $("#t-hero-title").value.trim(),
      heroDesc: $("#t-hero-desc").value.trim(),
      about: $("#t-about").value.trim()
    };
    localStorage.setItem("avra_texts", JSON.stringify(texts));
    applyTexts();
    showToast("Τα κείμενα αποθηκεύτηκαν");
  });
}
