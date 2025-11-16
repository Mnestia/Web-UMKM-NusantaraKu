<template>
  <footer id="kontak">
    <div class="footer-wave">
      <svg viewBox="0 0 1200 120" preserveAspectRatio="none">
        <path d="M0,0V46.29c47.79,22.2,103.59,32.17,158,28,70.36-5.37,136.33-33.31,206.8-37.5C438.64,32.43,512.34,53.67,583,72.05c69.27,18,138.3,24.88,209.4,13.08,36.15-6,69.85-17.84,104.45-29.34C989.49,25,1113-14.29,1200,52.47V0Z" opacity=".25" class="shape-fill"></path>
        <path d="M0,0V15.81C13,36.92,27.64,56.86,47.69,72.05,99.41,111.27,165,111,224.58,91.58c31.15-10.15,60.09-26.07,89.67-39.8,40.92-19,84.73-46,130.83-49.67,36.26-2.85,70.9,9.42,98.6,31.56,31.77,25.39,62.32,62,103.63,73,40.44,10.79,81.35-6.69,119.13-24.28s75.16-39,116.92-43.05c59.73-5.85,113.28,22.88,168.9,38.84,30.2,8.66,59,6.17,87.09-7.5,22.43-10.89,48-26.93,60.65-49.24V0Z" opacity=".5" class="shape-fill"></path>
        <path d="M0,0V5.63C149.93,59,314.09,71.32,475.83,42.57c43-7.64,84.23-20.12,127.61-26.46,59-8.63,112.48,12.24,165.56,35.4C827.93,77.22,886,95.24,951.2,90c86.53-7,172.46-45.71,248.8-84.81V0Z" class="shape-fill"></path>
      </svg>
    </div>
    <div class="container">
      <div class="footer-content">
        <div class="footer-column">
          <h3>Tentang NusantaraKu</h3>
          <p>NusantaraKu adalah platform direktori digital yang menghubungkan masyarakat dengan UMKM terbaik di lingkungan sekitar Kampus UPNVJ.</p>
          <div class="social-links">
          <a href="https://facebook.com/" target="_blank"><i class="fab fa-facebook-f"> </i></a>
          <a href="https://twitter.com/" target="_blank"><i class="fab fa-twitter"></i></a>
          <a href="https://instagram.com/" target="_blank"><i class="fab fa-instagram"></i></a>
          <a href="https://linkedin.com/" target="_blank"><i class="fab fa-linkedin-in"></i></a>
          </div>
        </div>
        <div class="footer-column">
          <h3>Kategori Populer</h3>
          <ul class="footer-links">
            <li v-for="category in popularCategories" :key="category.id">
              <a :href="category.url">{{ category.text }}</a>
            </li>
          </ul>
        </div>
        <div class="footer-column">
          <h3>Kontak Kami</h3>
          <ul class="footer-links">
            <li><i class="fas fa-map-marker-alt"></i> Jl. Wibisono No 13 Halim, Jakarta</li>
            <li><i class="fas fa-phone"></i> +62 812-2173-7231</li>
            <li><i class="fas fa-envelope"></i> tjtnusantara@gmail.com</li>
          </ul>
        </div>
      </div>
      <div class="copyright">
        <p>&copy; 2025 TJT Creative. Semua Hak Dilindungi.</p>
      </div>
    </div>
  </footer>
</template>

<script>
export default {
  name: 'Footer',
  data() {
    return {
      popularCategories: [
        { id: 1, text: 'Kuliner' },
        { id: 2, text: 'Jasa'},
        { id: 3, text: 'Fashion'},
        { id: 4, text: 'Home Living'},
        { id: 5, text: 'Kesehatan'}
      ]
    }
  }
}
</script>

<style scoped>
footer {
  background: linear-gradient(135deg, #0a0a2a 0%, #1a1a4a 25%, #0c1445 50%, #1a237e 75%, #283593 100%);
  color: white;
  padding: 0 0 20px;
  position: relative;
  overflow: hidden;
}

/* Efek bintang */
footer::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: 
    radial-gradient(white, rgba(255,255,255,.2) 2px, transparent 4px),
    radial-gradient(white, rgba(255,255,255,.15) 1px, transparent 3px),
    radial-gradient(white, rgba(255,255,255,.1) 2px, transparent 4px);
  background-size: 550px 550px, 350px 350px, 250px 250px;
  background-position: 0 0, 40px 60px, 130px 270px;
  z-index: 0;
}


.footer-wave .shape-fill {
  fill: #FFFFFF;
}

.container {
  position: relative;
  z-index: 1;
}

.footer-content {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 40px;
  margin-bottom: 50px;
  padding-top: 40px;
}

.footer-column h3 {
  font-size: 1.3rem;
  margin-bottom: 20px;
  position: relative;
  padding-bottom: 10px;
}

.footer-column h3::after {
  content: '';
  position: absolute;
  left: 0;
  bottom: 0;
  width: 50px;
  height: 2px;
  background: linear-gradient(90deg, #4fc3f7, #29b6f6);
  border-radius: 2px;
}

.footer-links {
  list-style: none;
}

.footer-links li {
  margin-bottom: 10px;
  display: flex;
  align-items: center;
}

.footer-links a {
  color: white;
  text-decoration: none;
  transition: all 0.3s ease;
  position: relative;
}

.footer-links a::after {
  content: '';
  position: absolute;
  bottom: -2px;
  left: 0;
  width: 0;
  height: 1px;
  background: linear-gradient(90deg, #4fc3f7, #29b6f6);
  transition: width 0.3s ease;
}

.footer-links a:hover {
  color: #bbdefb;
}

.footer-links a:hover::after {
  width: 100%;
}

.footer-links i {
  margin-right: 10px;
  color: #4fc3f7;
  width: 16px;
  text-align: center;
}

.social-links {
  display: flex;
  gap: 15px;
  margin-top: 20px;
}

.social-links a {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 40px;
  height: 40px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 50%;
  color: white;
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
}

.social-links a::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.2), transparent);
  transition: left 0.5s;
}

.social-links a:hover::before {
  left: 100%;
}

.social-links a:hover {
  background: rgba(79, 195, 247, 0.8);
  transform: translateY(-3px);
  box-shadow: 0 5px 15px rgba(41, 182, 246, 0.4);
}

.copyright {
  text-align: center;
  padding-top: 20px;
  border-top: 1px solid rgba(255, 255, 255, 0.1);
  color: rgba(255, 255, 255, 0.7);
  font-size: 0.9rem;
}

@media (max-width: 768px) {
  .footer-content {
    grid-template-columns: 1fr;
    gap: 30px;
  }
  
  .footer-wave svg {
    height: 60px;
  }
}
</style>