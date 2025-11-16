<template>
  <section class="hero" id="beranda">
    <div class="background-slideshow">
      <div 
        v-for="(bg, index) in backgrounds" 
        :key="index"
        :class="['slide', { 'active': currentSlide === index }]"
        :style="{ backgroundImage: `url(${bg})` }"
      ></div>
    </div>
    
    <div class="container">
      <div class="hero-content">
        <h1 data-aos="fade-down" data-aos-duration="1000" data-aos-delay="200">
          Temukan UMKM Terbaik di Sekitar UPNVJ
        </h1>
        <p data-aos="fade-up" data-aos-duration="1000" data-aos-delay="400">
          Jelajahi ayam geprek legendaris, kedai bakso yang viral, tempat service andalan, dan berbagai usaha lokal lainnya di lingkungan Kampus.
        </p>
        <button 
          class="cta-button" 
          data-aos="fade-up" 
          data-aos-duration="1000" 
          data-aos-delay="600"
          @click="scrollToCategories"
        >
          <span>Mulai Belanja</span>
          <i class="fas fa-arrow-right"></i>
        </button>
      </div>
    </div>

    <div class="slideshow-nav" data-aos="fade-up" data-aos-duration="1000" data-aos-delay="800">
      <div 
        v-for="(bg, index) in backgrounds" 
        :key="index"
        :class="['slideshow-dot', { 'active': currentSlide === index }]"
        @click="setSlide(index)"
      ></div>
    </div>
  </section>
</template>

<script>
export default {
  name: 'Hero',
  data() {
    return {
      currentSlide: 0,
      backgrounds: [
        'https://images.unsplash.com/photo-1555396273-367ea4eb4db5?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1974&q=80',
        'https://images.unsplash.com/photo-1565299624946-b28f40a0ae38?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1981&q=80',
        'https://images.unsplash.com/photo-1565958011703-44f9829ba187?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1965&q=80',
        'https://images.unsplash.com/photo-1568901346375-23c9450c58cd?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1998&q=80'
      ]
    }
  },
  mounted() {
    this.startSlideshow();
    // Refresh AOS setelah komponen dimount
    if (typeof AOS !== 'undefined') {
      AOS.refresh();
    }
  },
  beforeUnmount() {
    if (this.slideInterval) {
      clearInterval(this.slideInterval);
    }
  },
  methods: {
    scrollToCategories() {
      const categoriesSection = document.getElementById('kategori');
      if (categoriesSection) {
        categoriesSection.scrollIntoView({ 
          behavior: 'smooth',
          block: 'start'
        });
      }
    },
    startSlideshow() {
      this.slideInterval = setInterval(() => {
        this.currentSlide = (this.currentSlide + 1) % this.backgrounds.length;
      }, 5000);
    },
    setSlide(index) {
      this.currentSlide = index;
      // Reset interval setelah klik manual
      clearInterval(this.slideInterval);
      this.startSlideshow();
    }
  }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@700;800;900&display=swap');

.hero {
  background: var(--gradient);
  color: white;
  min-height: 100vh;
  position: relative;
  overflow: hidden;
  display: flex;
  align-items: center;
}

.background-slideshow {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.slide {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  opacity: 0;
  transition: opacity 1.5s ease-in-out;
}

.slide.active {
  opacity: 0.3;
}

.hero::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1440 320"><path fill="%23ffffff" fill-opacity="0.1" d="M0,224L48,213.3C96,203,192,181,288,181.3C384,181,480,203,576,192C672,181,768,139,864,138.7C960,139,1056,181,1152,181.3C1248,181,1344,139,1392,117.3L1440,96L1440,320L1392,320C1344,320,1248,320,1152,320C1056,320,960,320,864,320C768,320,672,320,576,320C480,320,384,320,288,320C192,320,96,320,48,320L0,320Z"></path></svg>');
  background-size: cover;
  background-position: center;
  z-index: 1;
}

.container {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
  position: relative;
  z-index: 2;
}

.hero-content {
  max-width: 800px;
  margin: 0 auto;
  text-align: center;
}

.hero h1 {
  font-family: 'Inter', sans-serif;
  font-weight: 400;
  font-size: 4rem;
  letter-spacing: -0.03em;
  line-height: 1.1;
}

.hero p {
  font-size: 1.2rem;
  margin-bottom: 40px;
  opacity: 0.95;
  text-shadow: 0 1px 5px rgba(0, 0, 0, 0.2);
  line-height: 1.6;
}

/* CTA Button Styles */
.cta-button {
  background: white;
  color: var(--dark);
  border: none;
  padding: 18px 40px;
  border-radius: 50px;
  font-size: 1.2rem;
  font-weight: 600;
  cursor: pointer;
  display: inline-flex;
  align-items: center;
  gap: 15px;
  transition: all 0.4s cubic-bezier(0.25, 0.46, 0.45, 0.94);
  box-shadow: 
    0 10px 30px rgba(0, 0, 0, 0.2),
    0 0 0 0 rgba(255, 255, 255, 0.3);
  position: relative;
  overflow: hidden;
}

.cta-button::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.4), transparent);
  transition: left 0.8s ease;
}

.cta-button:hover {
  transform: translateY(-5px);
  box-shadow: 
    0 15px 40px rgba(0, 0, 0, 0.3),
    0 0 0 15px rgba(255, 255, 255, 0.1);
  color: var(--primary);
}

.cta-button:hover::before {
  left: 100%;
}

.cta-button:hover i {
  transform: translateX(8px);
}

.cta-button:active {
  transform: translateY(-2px);
  box-shadow: 
    0 8px 25px rgba(0, 0, 0, 0.25),
    0 0 0 10px rgba(255, 255, 255, 0.15);
}

.cta-button i {
  transition: transform 0.3s ease;
  font-size: 1.1rem;
}

.slideshow-nav {
  position: absolute;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 10px;
  z-index: 2;
}

.slideshow-dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.5);
  cursor: pointer;
  transition: background 0.3s;
}

.slideshow-dot.active {
  background: white;
}

/* Animasi tambahan untuk smooth appearance */
.hero-content > * {
  opacity: 0;
  animation: fadeInUp 0.8s ease-out forwards;
}

.hero-content h1 {
  animation-delay: 0.2s;
}

.hero-content p {
  animation-delay: 0.4s;
}

.hero-content .cta-button {
  animation-delay: 0.6s;
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Pulse animation untuk button */
@keyframes pulseGlow {
  0% {
    box-shadow: 
      0 10px 30px rgba(0, 0, 0, 0.2),
      0 0 0 0 rgba(255, 255, 255, 0.3);
  }
  50% {
    box-shadow: 
      0 10px 30px rgba(0, 0, 0, 0.2),
      0 0 0 8px rgba(255, 255, 255, 0.1);
  }
  100% {
    box-shadow: 
      0 10px 30px rgba(0, 0, 0, 0.2),
      0 0 0 0 rgba(255, 255, 255, 0.3);
  }
}

.cta-button {
  animation: pulseGlow 3s ease-in-out infinite;
}

@media (max-width: 768px) {
  .hero h1 {
    font-size: 2.5rem;
  }
  
  .hero p {
    font-size: 1rem;
    margin-bottom: 30px;
  }
  
  .cta-button {
    padding: 16px 32px;
    font-size: 1.1rem;
  }
  
  .slide.active {
    opacity: 0.4; 
  }

  /* Adjust AOS delays untuk mobile */
  .hero h1[data-aos] {
    data-aos-delay: 100;
  }
  
  .hero p[data-aos] {
    data-aos-delay: 200;
  }
  
  .cta-button[data-aos] {
    data-aos-delay: 300;
  }
  
  .slideshow-nav[data-aos] {
    data-aos-delay: 400;
  }
}

@media (max-width: 576px) {
  .hero h1 {
    font-size: 2rem;
  }
  
  .hero p {
    font-size: 0.95rem;
    margin-bottom: 25px;
  }
  
  .cta-button {
    padding: 14px 28px;
    font-size: 1rem;
    gap: 12px;
  }
  
  .cta-button i {
    font-size: 1rem;
  }
}
</style>