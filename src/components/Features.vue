<template>
  <section class="features" id="umkm">
    <div class="container">
      <div class="section-title">
        <h2>Mengapa Memilih NusantaraKu?</h2>
        <p>Kami membantu menghubungkan Anda dengan usaha-usaha terbaik di lingkungan sekitar</p>
      </div>
      <div class="features-grid">
        <div class="feature-card" v-for="feature in features" :key="feature.id">
          <div class="feature-icon">
            <i :class="feature.icon"></i>
          </div>
          <h3>{{ feature.title }}</h3>
          <p>{{ feature.description }}</p>
        </div>
      </div>
    </div>
    <div class="simple-divider"></div>
    <div class="dashed-divider"></div>
  </section>
</template>

<script>
export default {
  name: 'Features',
  data() {
    return {
      features: [
        {
          id: 1,
          icon: 'fas fa-map-marked-alt',
          title: 'Peta Interaktif',
          description: 'Temukan lokasi UMKM dengan mudah melalui peta digital yang interaktif dan informatif.'
        },
        {
          id: 2,
          icon: 'fas fa-info-circle',
          title: 'Informasi Lengkap',
          description: 'Dapatkan informasi detail tentang jam operasional, menu, harga, dan ulasan dari pengunjung.'
        },
        {
          id: 3,
          icon: 'fas fa-mobile-alt',
          title: 'Responsif',
          description: 'Akses website kami dengan nyaman dari berbagai perangkat, baik desktop maupun mobile.'
        }
      ]
    }
  }
}
</script>

<style scoped>
.features {
  padding: 100px 0;
  background: white;
}

.dashed-divider {
    height: 1px;
    background: repeating-linear-gradient(
        90deg,
        #1e40af,
        #1e40af 5px,
        transparent 5px,
        transparent 10px
    );
    margin: 20px 0;
}

.features-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 30px;
}

.feature-card {
  background: white;
  border-radius: 10px;
  padding: 30px;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.05);
  transition: transform 0.3s, box-shadow 0.3s;
  text-align: center;
}

.feature-card:hover {
  transform: translateY(-10px);
  box-shadow: 0 15px 30px rgba(0, 0, 0, 0.1);
}

.feature-icon {
  width: 70px;
  height: 70px;
  background: var(--gradient);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 auto 20px;
  color: white;
  font-size: 1.8rem;
}

.feature-card h3 {
  font-size: 1.5rem;
  margin-bottom: 15px;
  color: var(--dark);
}

@media (max-width: 768px) {
  .features {
    padding: 70px 0;
  }
  
  .features-grid {
    grid-template-columns: 1fr;
    gap: 20px;
  }
}
</style>