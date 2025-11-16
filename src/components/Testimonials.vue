<template>
  <section class="testimonials" id="tentang">
    <div class="dashed-divider-top"></div>
    <div class="container">
      <div class="section-title">
        <h2>Apa Kata Pengguna?</h2>
        <p>Lihat pengalaman nyata dari pengguna yang telah menemukan UMKM terbaik melalui platform kami</p>
      </div>
      <div class="testimonials-container">
        <div 
          class="testimonial-card" 
          v-for="testimonial in testimonials" 
          :key="testimonial.id"
          :style="{ opacity: loaded ? 1 : 0, transform: loaded ? 'translateY(0)' : 'translateY(20px)' }"
        >
          <p class="testimonial-text">"{{ testimonial.text }}"</p>
          <div class="testimonial-author">
            <div class="author-avatar">{{ testimonial.initials }}</div>
            <div>
              <h4>{{ testimonial.name }}</h4>
              <p>{{ testimonial.role }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="simple-divider"></div>
  </section>
</template>

<script>
export default {
  name: 'Testimonials',
  data() {
    return {
      loaded: false,
      testimonials: [
        {
          id: 1,
          text: 'Berkat NusantaraKu, saya menemukan warung kopi tersembunyi yang menjadi favorit baru saya. Informasinya lengkap dan akurat!',
          name: 'Rayhan',
          role: 'Pengguna Baru',
          initials: 'RS'
        },
        {
          id: 2,
          text: 'Jujur, nusantaraku sangat sangat membantu, nusantaraku juga interaktif sekali.',
          name: 'Ali',
          role: 'Pengguna Baru',
          initials: 'AB'
        },
        {
          id: 3,
          text: 'Saya baru pindah ke daerah ini dan kesulitan menemukan tempat batagor yang enak, NusantaraKu sangat membantu mantap betul!',
          name: 'Vicky',
          role: 'Pengguna Baru',
          initials: 'VS'
        }
      ]
    }
  },
  mounted() {
    this.loaded = true
    window.addEventListener('scroll', this.handleScroll)
  },
  beforeUnmount() {
    window.removeEventListener('scroll', this.handleScroll)
  },
  methods: {
    handleScroll() {
      // Animation logic bisa ditambahkan di sini
    }
  }
}
</script>

<style scoped>
.testimonials {
  padding: 80px 0 100px 0; /* Top dikurangi, bottom tetap */
  background: white url('https://storage.googleapis.com/clevertap-assets/paper-lp/homepage/closingcta-supergraphic.webp');
  background-size: cover;
  background-position: center;
  position: relative;
  overflow: hidden;
}

.dashed-divider-top {
    height: 1px;
    background: repeating-linear-gradient(
        90deg,
        #1e40af,
        #1e40af 5px,
        transparent 5px,
        transparent 10px
    );
    margin: 0 0 40px 0; /* Top: 0, Bottom: 40px */
    width: 100%;
}

.simple-divider {
    height: 1px;
    background-color: #e0e0e0;
    margin: 40px 0 0 0; /* Top: 40px, Bottom: 0 */
    width: 100%;
}

.testimonials-container {
  display: flex;
  overflow-x: auto;
  gap: 30px;
  padding: 20px 0;
  scrollbar-width: thin;
}

.testimonial-card {
  min-width: 300px;
  background: #f9f9f9;
  border-radius: 10px;
  padding: 30px;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.05);
  transition: opacity 0.5s, transform 0.5s;
}

.testimonial-text {
  font-style: italic;
  margin-bottom: 20px;
}

.testimonial-author {
  display: flex;
  align-items: center;
}

.author-avatar {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background: var(--gradient);
  margin-right: 15px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-weight: bold;
}

@media (max-width: 768px) {
  .testimonials {
    padding: 60px 0 70px 0; /* Top dikurangi, bottom tetap */
  }
  
  .testimonial-card {
    min-width: 280px;
  }
}
</style>