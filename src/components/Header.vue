<template>
  <header :class="{ 'scrolled': isScrolled, 'transparent': !isScrolled }" id="header">
    <div class="container">
      <nav class="navbar">
        <a href="#" class="logo" @click.prevent="scrollToTop">
          <img src="@/assets/logo.png" alt="TJT CREATIVE" class="logo-image">
          <span class="logo-text">NUSANTARAKU</span>
        </a>
        <ul class="nav-links" :class="{ 'active': mobileMenuActive }">
          <li><a href="#beranda" @click.prevent="scrollToSection('beranda')">Beranda</a></li>
          <li><a href="#umkm" @click.prevent="scrollToSection('umkm')">Fitur</a></li>
          <li><a href="#kategori" @click.prevent="scrollToSection('kategori')">Kategori</a></li>
          <li><a href="#tentang" @click.prevent="scrollToSection('tentang')">Tentang</a></li>
          <li><a href="#kontak" @click.prevent="scrollToSection('kontak')">Kontak</a></li>
        </ul>
        <button class="cta-button" @click="scrollToSection('daftar')">Daftarkan UMKM</button>
        <div class="mobile-menu" @click="toggleMobileMenu">
          <i class="fas fa-bars"></i>
        </div>
      </nav>
    </div>
  </header>
</template>

<script>
export default {
  name: 'Header',
  data() {
    return {
      isScrolled: false,
      mobileMenuActive: false
    }
  },
  mounted() {
    window.addEventListener('scroll', this.handleScroll)
  },
  beforeUnmount() {
    window.removeEventListener('scroll', this.handleScroll)
  },
  methods: {
    handleScroll() {
      this.isScrolled = window.scrollY > 50
    },
    toggleMobileMenu() {
      this.mobileMenuActive = !this.mobileMenuActive
    },
    
    scrollToSection(sectionId) {
      // Tutup mobile menu jika terbuka
      this.mobileMenuActive = false
      
      // Gunakan setTimeout untuk memastikan Vue sudah render
      setTimeout(() => {
        const element = document.getElementById(sectionId)
        if (element) {
          const headerHeight = 80
          const elementPosition = element.getBoundingClientRect().top
          const offsetPosition = elementPosition + window.pageYOffset - headerHeight

          window.scrollTo({
            top: offsetPosition,
            behavior: 'smooth'
          })
        } else {
          console.warn(`Element dengan id ${sectionId} tidak ditemukan`)
        }
      }, 100)
    },
    
scrollToTop() {
  this.mobileMenuActive = false;
  
  // Jika sudah di paling atas, refresh halaman
  if (window.pageYOffset === 0) {
    window.location.reload();
  } else {
    // Jika tidak di paling atas, scroll ke atas dulu
    window.scrollTo({
      top: 0,
      behavior: 'smooth'
    });
    
    // Set timeout untuk refresh setelah scroll selesai
    setTimeout(() => {
      if (window.pageYOffset === 1) {
        window.location.reload();
      }
    }, 500);
  }

    }
  }
}
</script>

<style scoped>
header {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 1000;
  transition: all 0.4s ease;
  padding: 20px 0;
}

header.transparent {
  background: transparent;
  box-shadow: none;
}

header.scrolled {
  background: rgba(255, 255, 255, 0.95);
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.1);
  padding: 15px 0;
}

.container {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.logo {
  display: flex;
  align-items: center;
  text-decoration: none;
  cursor: pointer;
  gap: 12px;
}

.logo-image {
  height: 65px;
  width: auto;
  object-fit: contain;
  transition: all 0.3s ease;
}

.logo-text {
  font-size: 1.2rem;
  font-weight: 650;
  color: white;
  transition: color 0.3s;
}

header.scrolled .logo-text {
  color: var(--dark);
}

.nav-links {
  display: flex;
  list-style: none;
}

.nav-links li {
  margin-left: 30px;
}

.nav-links a {
  text-decoration: none;
  color: white;
  font-weight: 500;
  transition: color 0.3s;
  cursor: pointer;
  position: relative;
}

header.scrolled .nav-links a {
  color: var(--dark);
}

.nav-links a:hover {
  color: rgba(255, 255, 255, 0.8);
}

header.scrolled .nav-links a:hover {
  color: var(--primary);
}

.nav-links a::after {
  content: '';
  position: absolute;
  width: 0;
  height: 2px;
  bottom: -5px;
  left: 0;
  background: rgba(255, 255, 255, 0.8);
  transition: width 0.3s ease;
}

header.scrolled .nav-links a::after {
  background: var(--gradient);
}

.nav-links a:hover::after {
  width: 100%;
}

.cta-button {
  background: rgba(255, 255, 255, 0.9);
  color: var(--dark);
  border: none;
  padding: 10px 20px;
  border-radius: 30px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
}

header.scrolled .cta-button {
  background: var(--gradient);
  color: white;
}

.cta-button:hover {
  transform: translateY(-3px);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.2);
}

.mobile-menu {
  display: none;
  font-size: 1.5rem;
  cursor: pointer;
  color: white;
}

header.scrolled .mobile-menu {
  color: var(--dark);
}

@media (max-width: 992px) {
  .nav-links {
    display: none;
    position: absolute;
    top: 100%;
    left: 0;
    width: 100%;
    background: white;
    flex-direction: column;
    padding: 20px;
    box-shadow: 0 5px 10px rgba(0, 0, 0, 0.1);
  }
  
  .nav-links.active {
    display: flex;
  }
  
  .nav-links li {
    margin: 10px 0;
  }
  
  .nav-links a {
    color: var(--dark);
  }
  
  .mobile-menu {
    display: block;
  }
}

@media (max-width: 768px) {
  .logo-text {
    font-size: 1.5rem;
  }
  
  .cta-button {
    padding: 8px 16px;
    font-size: 0.9rem;
  }
}

@media (max-width: 576px) {
  .logo-text {
    font-size: 1.3rem;
  }
  
  .navbar {
    padding: 15px 0;
  }
}
</style>