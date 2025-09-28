[Untitled-2.html](https://github.com/user-attachments/files/22585624/Untitled-2.html)[Upl
  <script>
    /* Données d'exemple : ajoute / modifie pour avoir tes articles */
    const articles = [
      { id: 1, title: "Titre article 1", summary: "Résumé court du premier article.", date: "2025-09-28", content: "Contenu complet du premier article.\nTu peux mettre du texte long ici." },
      { id: 2, title: "Titre article 2", summary: "Résumé court du deuxième article.", date: "2025-09-20", content: "Contenu complet du deuxième article."},
      { id: 3, title: "Article spécial", summary: "Une description sympa qui donne envie.", date: "2025-09-10", content: "Contenu du troisième article.\nInclure images ? ajoute une balise <img> dans le modalContent."}
    ];

    const articlesGrid = document.getElementById('articlesGrid');
    const modalBackdrop = document.getElementById('modalBackdrop');
    const modalTitle = document.getElementById('modalTitle');
    const modalContent = document.getElementById('modalContent');
    const modalMeta = document.getElementById('modalMeta');
    const btnBuyNow = document.getElementById('btnBuyNow');

    // Remplir la grille d'articles
    function renderArticles(){
      articlesGrid.innerHTML = '';
      for(const a of articles){
        const card = document.createElement('article');
        card.className = 'card';
        card.innerHTML = `
          <h3>${escapeHtml(a.title)}</h3>
          <div class="meta">${a.date}</div>
          <p>${escapeHtml(a.summary)}</p>
          <div style="margin-top:10px;display:flex;gap:8px">
            <button class="btn" onclick="openArticle(${a.id})">Lire</button>
            <button class="btn secondary" onclick="scrollToForm(${a.id})">Commander</button>
          </div>
        `;
        articlesGrid.appendChild(card);
      }
    }

    function openArticle(id){
      const a = articles.find(x => x.id === id);
      if(!a) return;
      modalTitle.textContent = a.title;
      modalMeta.textContent = `Publié le ${a.date}`;
      modalContent.textContent = a.content;
      modalBackdrop.style.display = 'flex';
      modalBackdrop.setAttribute('aria-hidden','false');

      // Au clic sur "Commander cet article", on scrolle vers le formulaire et pré-remplit un message
      btnBuyNow.onclick = () => {
        closeModal();
        scrollToForm(id);
        prefillOrderFromArticle(id);
      };
    }
    function closeModal(){
      modalBackdrop.style.display = 'none';
      modalBackdrop.setAttribute('aria-hidden','true');
    }
    document.getElementById('closeModal').addEventListener('click', closeModal);
    modalBackdrop.addEventListener('click', (e)=>{ if(e.target===modalBackdrop) closeModal(); });

    // Form handling (client-side only)
    function handleSubmit(event){
      event.preventDefault();
      const form = document.getElementById('orderForm');
      const data = {
        nom: form.nom.value.trim(),
        prenom: form.prenom.value.trim(),
        age: form.age.value,
        telephone: form.telephone.value.trim(),
        adresse: form.adresse.value.trim(),
        email: form.email.value.trim(),
        paypal: form.paypal.value.trim(),
        message: form.message.value.trim(),
        dateCommande: new Date().toISOString()
      };

      // Validation simple
      if(!data.nom || !data.prenom || !data.email || !data.paypal){
        alert('Merci de remplir les champs obligatoires.');
        return false;
      }

      // Ici -> envoi au serveur par fetch (exemple). Actuellement pointe vers /submit (à remplacer).
      // Si tu n'as pas de serveur, tu peux configurer un service comme Google Sheets, Airtable, ou Zapier pour recevoir les données.
      fetch('/submit', {
        method:'POST',
        headers:{'Content-Type':'application/json'},
        body: JSON.stringify(data)
      })
      .then(res => {
        if(res.ok){
          alert('Commande envoyée ! Nous vous contactons bientôt.');
          form.reset();
        } else {
          // si pas de serveur, on tombe ici
          alert('Formulaire traité côté client — pas de backend trouvé. Voir la console pour les données.');
          console.log('Données commande (simulées):', data);
        }
      })
      .catch(err=>{
        // Pas de serveur actif : afficher données dans console
        console.warn('Échec envoi (pas de backend). Données affichées localement :', data);
        alert('Pas de backend détecté. Les données ont été affichées dans la console du navigateur.');
        console.log('Données commande (simulées):', data);
      });

      return false;
    }

    // Prérenseigner le formulaire quand on vient d'un article
    function prefillOrderFromArticle(articleId){
      const a = articles.find(x => x.id === articleId);
      if(!a) return;
      const form = document.getElementById('orderForm');
      form.message.value = `Je souhaite commander : "${a.title}" — ${a.summary}`;
      scrollToForm();
    }

    // Scroll vers le formulaire (optionnel : id article)
    function scrollToForm(id){
      const el = document.getElementById('orderSection');
      if(!el) return;
      el.scrollIntoView({behavior:'smooth', block:'start'});
      if(id) prefillOrderFromArticle(id);
    }

    // Petite fonction pour éviter injection (affichage simple)
    function escapeHtml(str){ return String(str).replace(/[&<>"']/g, function(m){ return ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":"&#39;"})[m]; }); }

    // Init
    renderArticles();

    // ----- NOTES pour intégrer PayPal (optionnel) -----
    // 1) Pour les paiements réels, crée un compte PayPal Business et génère un "Client ID".
    // 2) Remplace le script ci-dessous avec ton client-id :
    //    <script src="https://www.paypal.com/sdk/js?client-id=YOUR_PAYPAL_CLIENT_ID&currency=USD"></script>
     <script src="https://www.paypal.com/sdk/js?client-id=YOUR_PAYPAL_CLIENT_ID&currency=USD"></script>
     <div id="paypal-button-container"></div>
     <script>
    //   paypal.Buttons({
    //     createOrder: function(data, actions) { return actions.order.create({ purchase_units: [{ amount: { value: '10.00' } }] }); },
    //     onApprove: function(data, actions) { return actions.order.capture().then(function(details) { alert('Paiement reçu: ' + details.payer.name.given_name); /* envoyer au serveur */ }); }
    //   }).render('#paypal-button-container');
    // </script>
    <!doctype html>
<html lang="fr">
<head>
<head>
  <meta charset="UTF-8">
  <title>Mon site vitrine</title>

  <style>
/* === Corps de page === */
body {
  background: var(--bg-gradient);
  color: var(--text);
  font-family: var(--font);
  line-height: 1.6;
  margin: 0;
  padding: 20px;
}

/* === Header === */
header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 0;
  background: var(--primary);
  border-radius: var(--radius);
  color: #fff;
  box-shadow: var(--shadow-light);
  transition: var(--transition);
}
header h1 {
  font-size: 2rem;
  margin: 0;
  cursor: default;
}
header .btn {
  background: #fff;
  color: var(--primary);
  border: none;
  padding: 10px 18px;
  border-radius: 10px;
  cursor: pointer;
  font-weight: bold;
  transition: var(--transition);
}
header .btn:hover {
  background: var(--secondary);
  color: #fff;
}

/* === Grille d’articles === */
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit,minmax(250px,1fr));
  gap: 25px;
  margin: 40px 0;
}
.article {
  background: var(--card);
  padding: 20px;
  border-radius: var(--radius);
  box-shadow: var(--shadow-light);
  transition: transform var(--transition), box-shadow var(--transition);
  position: relative;
  overflow: hidden;
}
.article:hover {
  transform: translateY(-6px);
  box-shadow: var(--shadow-hover);
}
.article h2 {
  color: var(--primary);
  margin-bottom: 10px;
  transition: color var(--transition);
}
.article:hover h2 {
  color: var(--secondary);
}
.article p {
  color: var(--muted);
}

/* === Formulaire === */
form {
  background: var(--card);
  padding: 30px;
  border-radius: var(--radius);
  box-shadow: var(--shadow-light);
  max-width: 600px;
  margin: 0 auto 50px;
  transition: var(--transition);
}
form h2 {
  margin-bottom: 20px;
  color: var(--primary);
}
input, textarea {
  width: 100%;
  padding: 12px;
  margin: 10px 0;
  border-radius: 8px;
  border: 1px solid #ccc;
  transition: var(--transition);
}
input:focus, textarea:focus {
  border-color: var(--secondary);
  box-shadow: 0 0 8px rgba(66,165,245,0.4);
  outline: none;
}
button.submit-btn {
  background: var(--primary);
  color: #fff;
  border: none;
  padding: 14px 20px;
  border-radius: 10px;
  cursor: pointer;
  font-weight: bold;
  transition: var(--transition);
}
button.submit-btn:hover {
  background: var(--secondary);
}

/* === Footer === */
footer {
  text-align: center;
  color: var(--muted);
  font-size: 0.85rem;
  margin-top: 40px;
}

/* === Liens === */
a {
  color: var(--primary);
  text-decoration: none;
  transition: var(--transition);
}
a:hover {
  text-decoration: underline;
}

/* === Animations sur les cartes === */
.article::before {
  content: '';
  position: absolute;
  width: 120%;
  height: 120%;
  background: rgba(66,165,245,0.1);
  top: -110%;
  left: -110%;
  transform: rotate(45deg);
  transition: transform var(--transition);
}
.article:hover::before {
  transform: rotate(45deg) translate(80px, 80px);
}

/* === Responsive === */
@media(max-width:600px) {
  header { flex-direction: column; gap: 15px; text-align: center; }
  .grid { grid-template-columns: 1fr; }
}

    :root {
      --bg-gradient: linear-gradient(135deg, #f0f4f8, #e3f2fd);
      --primary: #1565c0;
      --secondary: #42a5f5;
      --card: #ffffff;
      --shadow-light: 0 6px 18px rgba(0,0,0,0.08);
      --shadow-hover: 0 12px 30px rgba(0,0,0,0.15);
      --radius: 14px;
      --text: #0f172a;
      --muted: #6b7280;
      --transition: 0.3s;
      --font: 'Segoe UI', Arial, sans-serif;
    }

    body {
      background: var(--bg-gradient);
      color: var(--text);
      font-family: var(--font);
      line-height: 1.6;
      margin: 0;
      padding: 20px;
    }

    /* ... tout le reste du CSS ici ... */
  </style>
</head>

  </script>
</body>
</html>
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>Mon site vitrine pro</title>
  <style>
    /* === Variables couleurs === */
    :root {
      --bg-gradient: linear-gradient(135deg, #f0f4f8, #e3f2fd);
      --primary: #1565c0;
      --secondary: #42a5f5;
      --accent: #ff9800;
      --card: #ffffff;
      --radius: 14px;
      --shadow-light: 0 6px 18px rgba(0,0,0,0.08);
      --shadow-hover: 0 12px 30px rgba(0,0,0,0.15);
      --text: #0f172a;
      --muted: #6b7280;
      --transition: 0.3s;
      --font: 'Segoe UI', Arial, sans-serif;
    }

    /* === Base === */
    *{ box-sizing:border-box; margin:0; padding:0;}
    body{
      background: var(--bg-gradient);
      font-family: var(--font);
      line-height:1.6;
      color: var(--text);
    }
    a{ text-decoration:none; color: var(--primary);}
    a:hover{ color: var(--secondary); }

    /* === Bannière accueil === */
    .hero{
      text-align:center;
      padding:60px 20px;
      background: linear-gradient(90deg, #1565c0, #42a5f5);
      color:white;
      border-radius: var(--radius);
      margin-bottom:40px;
      animation: fadeIn 1.5s ease-in;
    }
    .hero h1{
      font-size:3rem;
      margin-bottom:15px;
      animation: bounce 2s infinite;
    }
    .hero p{
      font-size:1.2rem;
      margin-bottom:25px;
    }
    .hero button{
      background: var(--accent);
      color:white;
      border:none;
      padding:15px 25px;
      border-radius:12px;
      font-size:1rem;
      cursor:pointer;
      transition: 0.3s;
    }
    .hero button:hover{
      background: #ffb74d;
      transform: scale(1.05);
    }

    /* === Grille articles === */
    .grid{
      display:grid;
      grid-template-columns:repeat(auto-fit,minmax(250px,1fr));
      gap:25px;
      margin-bottom:40px;
      padding:0 10px;
    }
    .article{
      background:var(--card);
      padding:20px;
      border-radius: var(--radius);
      box-shadow: var(--shadow-light);
      transition: transform var(--transition), box-shadow var(--transition);
      position:relative;
      overflow:hidden;
    }
    .article:hover{
      transform: translateY(-8px);
      box-shadow: var(--shadow-hover);
    }
    .article h2{color:var(--primary); margin-bottom:10px;}
    .article p{color:var(--muted);}
    .article button{
      background: var(--secondary);
      color:white;
      border:none;
      padding:10px 15px;
      border-radius:8px;
      cursor:pointer;
      transition: 0.3s;
    }
    .article button:hover{
      background: var(--accent);
      transform: scale(1.05);
    }

    /* === Formulaire === */
    form{
      background:var(--card);
      padding:30px;
      border-radius: var(--radius);
      box-shadow: var(--shadow-light);
      max-width:600px;
      margin:0 auto 50px;
      transition: var(--transition);
      animation: fadeIn 1.5s ease-in;
    }
    form h2{margin-bottom:20px; color:var(--primary);}
    input, textarea{
      width:100%;
      padding:12px;
      margin:8px 0;
      border-radius:8px;
      border:1px solid #ccc;
      transition:0.3s;
    }
    input:focus, textarea:focus{
      border-color: var(--secondary);
      box-shadow: 0 0 8px rgba(66,165,245,0.4);
      outline:none;
    }
    button.submit-btn{
      background: var(--primary);
      color:white;
      border:none;
      padding:14px 20px;
      border-radius:12px;
      cursor:pointer;
      font-weight:bold;
      transition:0.3s;
    }
    button.submit-btn:hover{
      background: var(--secondary);
      transform: scale(1.05);
    }

    /* === Footer === */
    footer{
      text-align:center;
      color:var(--muted);
      font-size:0.85rem;
      margin-top:40px;
      padding-bottom:20px;
    }

    /* === Animations === */
    @keyframes bounce{
      0%,100%{ transform: translateY(0); }
      50%{ transform: translateY(-10px);}
    }
    @keyframes fadeIn{
      0%{opacity:0;}
      100%{opacity:1;}
    }

    /* === Responsive === */
    @media(max-width:600px){
      .grid{ grid-template-columns:1fr;}
      .hero h1{ font-size:2.2rem;}
    }
  </style>
</head>
<body>

  <!-- Bannière -->
  <div class="hero">
    <h1>Site vitrine</h1>
    <p>Le site qui te permettra de reponde à vos besoins.</p>
    <button onclick="document.getElementById('orderForm').scrollIntoView({behavior:'smooth'})">Commander maintenant</button>
  </div>

  <!-- Articles -->
  <div class="grid">
    <div class="article">
      <h2>Article 1</h2>
      <p>Exemple d’article super attractif pour ton client.</p>
      <button onclick="document.getElementById('orderForm').scrollIntoView({behavior:'smooth'})">Commander</button>
    </div>
    <div class="article">
      <h2>Article 2</h2>
      <p>Deuxième article avec un design moderne et pro.</p>
      <button onclick="document.getElementById('orderForm').scrollIntoView({behavior:'smooth'})">Commander</button>
    </div>
    <div class="article">
      <h2>Article 3</h2>
      <p>Troisième article qui impressionne tes acheteurs.</p>
      <button onclick="document.getElementById('orderForm').scrollIntoView({behavior:'smooth'})">Commander</button>
    </div>
  </div>

  <!-- Formulaire -->
  <form id="orderForm">
    <h2>Formulaire de commande</h2>
    <input type="text" placeholder="Nom" required>
    <input type="text" placeholder="Prénom" required>
    <input type="number" placeholder="Âge" required>
    <input type="email" placeholder="E-mail" required>
    <input type="text" placeholder="PayPal" required>
    <textarea placeholder="Message / précision"></textarea>
    <button type="submit" class="submit-btn">Envoyer la commande</button>
  </form>

  <footer>
    &copy; 2025 Mon site vitrine. Tous droits réservés.
  </footer>

</body>
</html>
<style>
/* === Variables couleurs === */
:root{
  --primary: #1565c0;
  --secondary: #42a5f5;
  --accent: #ff9800;
  --bg-gradient: linear-gradient(135deg,#e3f2fd,#f0f4f8);
  --card:#fff;
  --radius:16px;
  --shadow:0 6px 18px rgba(0,0,0,0.08);
  --shadow-hover:0 12px 30px rgba(0,0,0,0.2);
  --text:#0f172a;
  --muted:#6b7280;
  --transition:0.3s;
  --font:'Segoe UI',Arial,sans-serif;
}

/* === Base === */
*{margin:0;padding:0;box-sizing:border-box;}
body{
  font-family: var(--font);
  background: var(--bg-gradient);
  color: var(--text);
  line-height:1.6;
}

/* === Bannière Hero === */
.hero{
  text-align:center;
  padding:80px 20px;
  background: linear-gradient(90deg, var(--primary), var(--secondary));
  color:white;
  border-radius:var(--radius);
  margin-bottom:50px;
  position:relative;
  overflow:hidden;
}
.hero h1{
  font-size:3rem;
  margin-bottom:20px;
  animation: bounce 2s infinite;
}
.hero p{
  font-size:1.2rem;
  margin-bottom:30px;
}
.hero button{
  background: var(--accent);
  color:#fff;
  border:none;
  padding:15px 30px;
  border-radius:12px;
  font-size:1rem;
  cursor:pointer;
  transition: var(--transition);
}
.hero button:hover{
  transform: scale(1.1);
  background:#ffb74d;
}

/* === Grille Articles === */
.grid{
  display:grid;
  grid-template-columns:repeat(auto-fit,minmax(280px,1fr));
  gap:30px;
  padding:0 10px;
  margin-bottom:50px;
}
.article{
  background:var(--card);
  padding:25px;
  border-radius:var(--radius);
  box-shadow: var(--shadow);
  transition: transform var(--transition), box-shadow var(--transition);
  position:relative;
  overflow:hidden;
}
.article:hover{
  transform: translateY(-10px) scale(1.02);
  box-shadow: var(--shadow-hover);
}
.article h2{
  color:var(--primary);
  margin-bottom:10px;
  transition: var(--transition);
}
.article:hover h2{
  color:var(--secondary);
}
.article p{
  color:var(--muted);
}
.article button{
  background: var(--secondary);
  color:white;
  border:none;
  padding:10px 18px;
  border-radius:10px;
  cursor:pointer;
  transition: var(--transition);
}
.article button:hover{
  background: var(--accent);
  transform: scale(1.1);
}

/* === Formulaire === */
form{
  background:var(--card);
  padding:35px;
  border-radius: var(--radius);
  box-shadow: var(--shadow);
  max-width:650px;
  margin:0 auto 50px;
  transition: var(--transition);
  animation: fadeIn 1.5s ease-in;
}
form h2{margin-bottom:25px;color:var(--primary);}
input, textarea{
  width:100%;
  padding:14px;
  margin:10px 0;
  border-radius:10px;
  border:1px solid #ccc;
  transition:0.3s;
}
input:focus, textarea:focus{
  border-color: var(--secondary);
  box-shadow:0 0 10px rgba(66,165,245,0.4);
  outline:none;
}
button.submit-btn{
  background: var(--primary);
  color:#fff;
  border:none;
  padding:16px 25px;
  border-radius:12px;
  cursor:pointer;
  font-weight:bold;
  transition:0.3s;
}
button.submit-btn:hover{
  background: var(--secondary);
  transform: scale(1.05);
}

/* === Footer === */
footer{
  text-align:center;
  color:var(--muted);
  font-size:0.85rem;
  margin-top:40px;
  padding-bottom:20px;
}

/* === Animations === */
@keyframes bounce{
  0%,100%{ transform:translateY(0);}
  50%{ transform:translateY(-12px);}
}
@keyframes fadeIn{
  0%{opacity:0; transform: translateY(20px);}
  100%{opacity:1; transform: translateY(0);}
}

/* === Effet au scroll === */
[data-animate]{
  opacity:0;
  transform: translateY(20px);
  transition: all 0.8s ease-out;
}
[data-animate].visible{
  opacity:1;
  transform: translateY(0);
}

/* === Responsive === */
@media(max-width:600px){
  .grid{grid-template-columns:1fr;}
  .hero h1{font-size:2.2rem;}
}
</style>

<script>
// === Animation au scroll ===
const observer = new IntersectionObserver((entries)=>{
  entries.forEach(entry=>{
    if(entry.isIntersecting){
      entry.target.classList.add('visible');
    }
  });
},{threshold:0.2});
document.querySelectorAll('[data-animate]').forEach(el=>observer.observe(el));
</script>
<!-- ARTICLES avec modal -->
<div class="grid">
  <div class="article" data-animate>
    <h2>Article 1</h2>
    <p>Exemple d’article super attractif.</p>
    <button onclick="openModal('Article 1','Voici les détails complets de l\'article 1.')">Voir plus</button>
  </div>
  <div class="article" data-animate>
    <h2>Article 2</h2>
    <p>Deuxième article avec un design moderne.</p>
    <button onclick="openModal('Article 2','Voici les détails complets de l\'article 2.')">Voir plus</button>
  </div>
  <div class="article" data-animate>
    <h2>Article 3</h2>
    <p>Troisième article qui impressionne tes acheteurs.</p>
    <button onclick="openModal('Article 3','Voici les détails complets de l\'article 3.')">Voir plus</button>
  </div>
</div>

<!-- MODAL -->
<div id="modalBackdrop" style="display:none;position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.6);justify-content:center;align-items:center;z-index:1000;">
  <div id="modalContent" style="background:white;padding:30px;border-radius:16px;max-width:500px;width:90%;position:relative;transform:translateY(-50px);opacity:0;transition:0.4s;">
    <h2 id="modalTitle"></h2>
    <p id="modalText"></p>
    <button style="margin-top:20px;background:#1565c0;color:white;padding:12px 20px;border:none;border-radius:12px;cursor:pointer;" onclick="scrollToForm()">Commander</button>
    <span style="position:absolute;top:10px;right:15px;font-size:20px;cursor:pointer;" onclick="closeModal()">✕</span>
  </div>
</div>

<script>
/* Ouvrir modal */
function openModal(title,text){
  document.getElementById('modalTitle').innerText = title;
  document.getElementById('modalText').innerText = text;
  const backdrop = document.getElementById('modalBackdrop');
  const content = document.getElementById('modalContent');
  backdrop.style.display = 'flex';
  setTimeout(()=>{ content.style.transform='translateY(0)'; content.style.opacity='1'; },50);
}

/* Fermer modal */
function closeModal(){
  const backdrop = document.getElementById('modalBackdrop');
  const content = document.getElementById('modalContent');
  content.style.transform='translateY(-50px)';
  content.style.opacity='0';
  setTimeout(()=>{ backdrop.style.display='none'; },400);
}

/* Scroll vers formulaire */
function scrollToForm(){
  document.getElementById('orderForm').scrollIntoView({behavior:'smooth'});
  closeModal();
}

/* Animation au scroll des articles */
const observer = new IntersectionObserver((entries)=>{
  entries.forEach(entry=>{
    if(entry.isIntersecting){ entry.target.classList.add('visible'); }
  });
},{threshold:0.2});
document.querySelectorAll('[data-animate]').forEach(el=>observer.observe(el));
</script>
<script>
// Validation simple pour le formulaire
document.querySelector('form').addEventListener('submit', function(e){
  const inputs = this.querySelectorAll('input, textarea');
  let safe = true;

  inputs.forEach(input => {
    const value = input.value;
    // Empêche les caractères suspects
    if(/[<>\/\\{}$]/.test(value)){
      alert("Merci d'éviter les caractères spéciaux dans le formulaire !");
      safe = false;
    }
  });

  if(!safe) e.preventDefault(); // Stop le submit si dangereux
});
</script>
<script>
// Empêche clic droit
document.addEventListener('contextmenu', event => event.preventDefault());

// Empêche F12 / Ctrl+Shift+I (console)
document.addEventListener('keydown', function(e) {
  if(e.key === "F12" || (e.ctrlKey && e.shiftKey && e.key === "I")) {
    e.preventDefault();
    alert("Action non autorisée !");
  }
});
// Correct
document.getElementById('modalText').textContent = userInput;

// Incorrect (risque XSS)
document.getElementById('modalText').innerHTML = userInput;
<input type="text" name="honeypot" style="display:none">

</script>


oading Untitled-2.html…]()
