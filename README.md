<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ShopFlow — Checkout</title>
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@300;400;500;600&family=DM+Serif+Display&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --bg: #f5f3ef;
    --surface: #ffffff;
    --border: #e8e4de;
    --text: #1a1816;
    --muted: #8a8480;
    --accent: #2d6a4f;
    --accent-light: #e8f4ed;
    --danger: #c94040;
    --danger-light: #fdf0f0;
    --price: #1a1816;
    --tag: #f0ede8;
  }

  body {
    font-family: 'DM Sans', sans-serif;
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    padding: 0;
  }

  /* HEADER */
  header {
    background: var(--surface);
    border-bottom: 1px solid var(--border);
    padding: 0 2rem;
    height: 60px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    position: sticky;
    top: 0;
    z-index: 100;
  }

  .logo {
    font-family: 'DM Serif Display', serif;
    font-size: 1.4rem;
    color: var(--text);
    letter-spacing: -0.02em;
  }

  .logo span { color: var(--accent); }

  .steps {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    font-size: 0.78rem;
    color: var(--muted);
  }

  .step { padding: 0.25rem 0.75rem; border-radius: 20px; }
  .step.active { background: var(--accent); color: white; font-weight: 500; }
  .step-sep { color: var(--border); }

  /* LAYOUT */
  .container {
    max-width: 1000px;
    margin: 2rem auto;
    padding: 0 1.5rem;
    display: grid;
    grid-template-columns: 1fr 380px;
    gap: 1.5rem;
    align-items: start;
  }

  /* CARD */
  .card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 1.75rem;
  }

  .card-title {
    font-size: 0.7rem;
    font-weight: 600;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 1.25rem;
  }

  /* FORM */
  .form-row { display: grid; grid-template-columns: 1fr 1fr; gap: 1rem; margin-bottom: 1rem; }
  .form-row.full { grid-template-columns: 1fr; }

  .field { display: flex; flex-direction: column; gap: 0.4rem; }
  .field label { font-size: 0.8rem; font-weight: 500; color: var(--muted); }

  .field input, .field select {
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 0.65rem 0.875rem;
    font-size: 0.9rem;
    font-family: 'DM Sans', sans-serif;
    background: var(--bg);
    color: var(--text);
    outline: none;
    transition: border-color 0.2s;
  }

  .field input:focus, .field select:focus { border-color: var(--accent); background: white; }

  /* COUPON */
  .coupon-row {
    display: flex;
    gap: 0.5rem;
    margin-top: 0.5rem;
  }

  .coupon-row input {
    flex: 1;
    border: 1.5px solid var(--border);
    border-radius: 8px;
    padding: 0.65rem 0.875rem;
    font-size: 0.875rem;
    font-family: 'DM Sans', sans-serif;
    background: white;
    color: var(--text);
    outline: none;
    transition: border-color 0.2s;
    letter-spacing: 0.05em;
    font-weight: 500;
    text-transform: uppercase;
  }

  .coupon-row input:focus { border-color: var(--accent); }
  .coupon-row input.success { border-color: var(--accent); background: var(--accent-light); }

  .btn-coupon {
    padding: 0.65rem 1.1rem;
    background: var(--text);
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 0.82rem;
    font-weight: 600;
    cursor: pointer;
    font-family: 'DM Sans', sans-serif;
    transition: opacity 0.2s;
    white-space: nowrap;
  }

  .btn-coupon:hover { opacity: 0.85; }

  .coupon-status {
    margin-top: 0.5rem;
    font-size: 0.8rem;
    font-weight: 500;
    display: none;
    align-items: center;
    gap: 0.4rem;
    padding: 0.5rem 0.75rem;
    border-radius: 6px;
  }

  .coupon-status.success {
    display: flex;
    background: var(--accent-light);
    color: var(--accent);
  }

  /* ORDER SUMMARY */
  .order-items { display: flex; flex-direction: column; gap: 1rem; margin-bottom: 1.25rem; }

  .order-item {
    display: flex;
    align-items: center;
    gap: 0.875rem;
  }

  .item-img {
    width: 52px;
    height: 52px;
    border-radius: 8px;
    background: var(--tag);
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 1.4rem;
    flex-shrink: 0;
    border: 1px solid var(--border);
  }

  .item-info { flex: 1; min-width: 0; }
  .item-name { font-size: 0.875rem; font-weight: 500; margin-bottom: 0.2rem; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
  .item-meta { font-size: 0.75rem; color: var(--muted); }
  .item-price { font-size: 0.9rem; font-weight: 600; color: var(--price); flex-shrink: 0; }

  .divider { height: 1px; background: var(--border); margin: 1rem 0; }

  .summary-row {
    display: flex;
    justify-content: space-between;
    font-size: 0.875rem;
    margin-bottom: 0.6rem;
    color: var(--muted);
  }

  .summary-row.discount { color: var(--accent); font-weight: 500; }
  .summary-row.total { color: var(--text); font-weight: 600; font-size: 1rem; margin-top: 0.75rem; padding-top: 0.75rem; border-top: 1px solid var(--border); }

  .discount-badge {
    display: none;
    background: var(--accent-light);
    color: var(--accent);
    font-size: 0.72rem;
    font-weight: 600;
    padding: 0.15rem 0.5rem;
    border-radius: 20px;
    margin-left: 0.4rem;
  }

  .discount-badge.visible { display: inline; }

  /* PAY BUTTON */
  .btn-pay {
    width: 100%;
    padding: 1rem;
    background: var(--accent);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 0.95rem;
    font-weight: 600;
    cursor: pointer;
    font-family: 'DM Sans', sans-serif;
    margin-top: 1.25rem;
    transition: all 0.2s;
    position: relative;
    letter-spacing: 0.01em;
  }

  .btn-pay:hover { background: #256040; }

  .btn-pay.broken {
    background: #c5bfb9;
    cursor: not-allowed;
    color: rgba(255,255,255,0.7);
  }

  .btn-pay.broken:hover { background: #c5bfb9; }

  /* BUG INDICATOR */
  .bug-banner {
    display: none;
    background: var(--danger-light);
    border: 1px solid #f0c0c0;
    border-radius: 8px;
    padding: 0.75rem 1rem;
    margin-top: 0.75rem;
    font-size: 0.8rem;
    color: var(--danger);
    align-items: flex-start;
    gap: 0.5rem;
  }

  .bug-banner.visible { display: flex; }
  .bug-banner strong { font-weight: 600; }

  .bug-icon { font-size: 1rem; flex-shrink: 0; margin-top: 0.05rem; }

  /* SECURITY NOTE */
  .security {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 0.4rem;
    font-size: 0.75rem;
    color: var(--muted);
    margin-top: 1rem;
  }

  /* SECTION LABEL */
  .section-label {
    font-size: 0.875rem;
    font-weight: 600;
    margin-bottom: 1rem;
    margin-top: 1.5rem;
    color: var(--text);
  }

  .section-label:first-child { margin-top: 0; }

  /* TAG */
  .tag {
    display: inline-flex;
    align-items: center;
    gap: 0.3rem;
    background: var(--tag);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 0.3rem 0.6rem;
    font-size: 0.75rem;
    color: var(--muted);
    margin-bottom: 1rem;
  }

  /* CLICK LOG */
  .click-log {
    display: none;
    background: #fff8e7;
    border: 1px solid #f0d080;
    border-radius: 8px;
    padding: 0.75rem 1rem;
    margin-top: 0.75rem;
    font-size: 0.78rem;
    color: #8a6800;
  }

  .click-log.visible { display: block; }
  .click-log .log-entry { margin-bottom: 0.2rem; font-family: monospace; }
  .click-log .log-label { font-weight: 600; font-size: 0.7rem; letter-spacing: 0.06em; text-transform: uppercase; margin-bottom: 0.4rem; }

  @media (max-width: 720px) {
    .container { grid-template-columns: 1fr; }
    .steps { display: none; }
  }
</style>
</head>
<body>

<header>
  <div class="logo">Shop<span>Flow</span></div>
  <div class="steps">
    <span class="step">Carrito</span>
    <span class="step-sep">›</span>
    <span class="step active">Checkout</span>
    <span class="step-sep">›</span>
    <span class="step">Confirmación</span>
  </div>
  <div style="width:80px"></div>
</header>

<div class="container">

  <!-- LEFT: FORM -->
  <div>
    <div class="card">
      <p class="section-label">Información de envío</p>

      <div class="form-row">
        <div class="field">
          <label>Nombre</label>
          <input type="text" value="Carlos" placeholder="Nombre">
        </div>
        <div class="field">
          <label>Apellido</label>
          <input type="text" value="Mendoza" placeholder="Apellido">
        </div>
      </div>

      <div class="form-row full">
        <div class="field">
          <label>Dirección</label>
          <input type="text" value="Av. Insurgentes Sur 1457, Col. Insurgentes Mixcoac" placeholder="Calle y número">
        </div>
      </div>

      <div class="form-row">
        <div class="field">
          <label>Ciudad</label>
          <input type="text" value="Ciudad de México" placeholder="Ciudad">
        </div>
        <div class="field">
          <label>Código postal</label>
          <input type="text" value="03920" placeholder="C.P.">
        </div>
      </div>

      <div class="form-row full">
        <div class="field">
          <label>Estado</label>
          <select>
            <option>Ciudad de México</option>
            <option>Jalisco</option>
            <option>Nuevo León</option>
          </select>
        </div>
      </div>

      <p class="section-label" style="margin-top:1.75rem">Método de pago</p>

      <div class="form-row full">
        <div class="field">
          <label>Número de tarjeta</label>
          <input type="text" value="4242 4242 4242 4242" placeholder="0000 0000 0000 0000">
        </div>
      </div>

      <div class="form-row">
        <div class="field">
          <label>Vencimiento</label>
          <input type="text" value="08 / 27" placeholder="MM / AA">
        </div>
        <div class="field">
          <label>CVV</label>
          <input type="text" value="•••" placeholder="CVV">
        </div>
      </div>

      <p class="section-label" style="margin-top:1.75rem">¿Tienes un cupón?</p>

      <div class="coupon-row">
        <input type="text" id="couponInput" placeholder="INGRESA TU CÓDIGO" maxlength="20">
        <button class="btn-coupon" onclick="applyCoupon()">Aplicar</button>
      </div>

      <div class="coupon-status success" id="couponStatus">
        ✓ Cupón DESCUENTO20 aplicado — 20% de descuento
      </div>

    </div>
  </div>

  <!-- RIGHT: ORDER SUMMARY -->
  <div>
    <div class="card">
      <p class="card-title">Resumen de orden</p>

      <div class="order-items">
        <div class="order-item">
          <div class="item-img">👟</div>
          <div class="item-info">
            <div class="item-name">Tenis Urban Run Pro</div>
            <div class="item-meta">Talla 28 · Negro</div>
          </div>
          <div class="item-price">$1,299</div>
        </div>
        <div class="order-item">
          <div class="item-img">🎒</div>
          <div class="item-info">
            <div class="item-name">Mochila Trail Explorer</div>
            <div class="item-meta">25L · Gris</div>
          </div>
          <div class="item-price">$849</div>
        </div>
        <div class="order-item">
          <div class="item-img">🧢</div>
          <div class="item-info">
            <div class="item-name">Gorra Deportiva SF</div>
            <div class="item-meta">Talla única · Verde</div>
          </div>
          <div class="item-price">$299</div>
        </div>
      </div>

      <div class="divider"></div>

      <div class="summary-row">
        <span>Subtotal (3 artículos)</span>
        <span>$2,447</span>
      </div>
      <div class="summary-row">
        <span>Envío</span>
        <span>$99</span>
      </div>
      <div class="summary-row discount" id="discountRow" style="display:none">
        <span>Descuento (DESCUENTO20) <span class="discount-badge visible">−20%</span></span>
        <span id="discountAmt">−$489</span>
      </div>
      <div class="summary-row total">
        <span>Total</span>
        <span id="totalAmt">$2,546</span>
      </div>

      <button class="btn-pay" id="payBtn" onclick="tryPay()">
        Pagar ahora
      </button>

      <div class="bug-banner" id="bugBanner">
        <span class="bug-icon">⚠️</span>
        <div>
          <strong>El botón no responde.</strong> Se aplicó el cupón correctamente pero al presionar "Pagar ahora" no ocurre ninguna acción. Sin error visible para el usuario.
        </div>
      </div>

      <div class="click-log" id="clickLog">
        <div class="log-label">Console log</div>
        <div class="log-entry" id="logEntry1"></div>
        <div class="log-entry" id="logEntry2"></div>
        <div class="log-entry" id="logEntry3"></div>
      </div>

      <div class="security">
        🔒 Pago cifrado con SSL · Powered by Conekta
      </div>

    </div>
  </div>

</div>

<script>
  let couponApplied = false;
  let clickCount = 0;

  function applyCoupon() {
    const input = document.getElementById('couponInput');
    const status = document.getElementById('couponStatus');
    const discountRow = document.getElementById('discountRow');
    const totalAmt = document.getElementById('totalAmt');
    const payBtn = document.getElementById('payBtn');
    const code = input.value.trim().toUpperCase();

    if (code === 'DESCUENTO20') {
      input.classList.add('success');
      status.classList.add('success');
      discountRow.style.display = 'flex';
      totalAmt.textContent = '$2,057';
      couponApplied = true;

      // Bug: el botón queda "roto" visualmente después de aplicar el cupón
      payBtn.classList.add('broken');
      payBtn.textContent = 'Pagar ahora';

      input.disabled = true;
    } else if (code === '') {
      input.style.borderColor = '#c94040';
      setTimeout(() => { input.style.borderColor = ''; }, 1500);
    } else {
      input.style.borderColor = '#c94040';
      status.style.display = 'none';
      setTimeout(() => { input.style.borderColor = ''; }, 1500);
    }
  }

  function tryPay() {
    const payBtn = document.getElementById('payBtn');
    const bugBanner = document.getElementById('bugBanner');
    const clickLog = document.getElementById('clickLog');

    if (couponApplied) {
      // BUG: el botón no hace nada — solo muestra el banner de debug
      clickCount++;
      bugBanner.classList.add('visible');
      clickLog.classList.add('visible');

      const now = new Date();
      const ts = now.toTimeString().split(' ')[0];

      document.getElementById('logEntry1').textContent =
        `[${ts}] onClick → checkout.pay() called`;
      document.getElementById('logEntry2').textContent =
        `[${ts}] couponState: applied | handler: undefined ← ERROR`;
      document.getElementById('logEntry3').textContent =
        `[${ts}] btn click #${clickCount} — no navigation triggered`;

      // Animación de "nada pasa" — botón no reacciona
      payBtn.style.transform = 'scale(0.99)';
      setTimeout(() => { payBtn.style.transform = ''; }, 100);

    } else {
      // Sin cupón funciona normalmente
      payBtn.textContent = 'Procesando...';
      payBtn.style.background = '#256040';
      payBtn.disabled = true;
      setTimeout(() => {
        payBtn.textContent = '✓ Pago completado';
        payBtn.style.background = '#1a4a30';
      }, 1800);
    }
  }

  // Allow pressing Enter in coupon field
  document.getElementById('couponInput').addEventListener('keydown', function(e) {
    if (e.key === 'Enter') applyCoupon();
  });
</script>

</body>
</html>
