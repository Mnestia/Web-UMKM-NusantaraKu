<template>
  <div id="app">
    <Header />
    
    <main>
      <section id="beranda" class="page-section">
        <Hero />
      </section>
      
      <section id="umkm" class="page-section">
        <Features />
      </section>
      
      <section id="kategori" class="page-section">
        <Categories />
      </section>
      
      <section id="tentang" class="page-section">
        <Testimonials />
      </section>
      
      <section id="daftar" class="page-section">
        <CtaSection />
      </section>
      
      <section id="kontak" class="page-section">
        <Footer />
      </section>
    </main>
  </div>
</template>

<script>
import Header from './components/Header.vue'
import Hero from './components/Hero.vue'
import Features from './components/Features.vue'
import Categories from './components/Categories.vue'
import Testimonials from './components/Testimonials.vue'
import CtaSection from './components/CtaSection.vue'
import Footer from './components/Footer.vue'
import AOS from 'aos';
import 'aos/dist/aos.css'; // You can also use <link> for styles
// ..
AOS.init();

export default {
  name: 'App',
  components: {
    Header,
    Hero,
    Features,
    Categories,
    Testimonials,
    CtaSection,
    Footer
  }
}
</script>

<style>
/* SMOOTH SCROLL GLOBAL */
html {
  scroll-behavior: smooth;
}

/* Pastikan sections punya scroll margin untuk header fixed */
.page-section {
  scroll-margin-top: 80px;
}

/* Optional: Tambah transition untuk semua sections */
.page-section {
  transition: all 0.3s ease;
}
</style>