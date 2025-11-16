<template>
  <section class="cta-section" id="daftar">
    <div class="container">
      <h2>Siap Bergabung dengan Kami?</h2>
      <p>Daftarkan UMKM Anda sekarang dan jangkau lebih banyak pelanggan di lingkungan sekitar</p>
      <button class="cta-button" @click="handleRegistration">Daftarkan UMKM Sekarang</button>
    </div>
  </section>
</template>

<script>
export default {
  name: 'CtaSection',
  methods: {
    handleRegistration() {
      this.$emit('register-clicked')
    }
  }
}
</script>

<style scoped>
.cta-section {
  padding: 100px 0;
  background: white;
  background-size: cover;
  text-align: center;
  position: relative;
  overflow: hidden;
  border-radius: 15px;
}

.cta-section::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(255, 255, 255, 0.85);
  z-index: 1;
}

.cta-section .container {
  position: relative;
  z-index: 2;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
}

.cta-section h2 {
  font-size: 2.5rem;
  margin-bottom: 20px;
  color: var(--dark);
  background: var(--gradient);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.cta-section p {
  max-width: 600px;
  margin: 0 auto 30px;
  font-size: 1.1rem;
  color: var(--dark);
  opacity: 0.8;
}

.cta-button {
  background: var(--gradient);
  color: white;
  border: none;
  padding: 15px 30px;
  border-radius: 8px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.4s cubic-bezier(0.25, 0.46, 0.45, 0.94);
  box-shadow: 0 4px 15px rgba(52, 152, 219, 0.3);
  font-size: 1.1rem;
}

.cta-button:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 25px rgba(52, 152, 219, 0.4);
}

@media (max-width: 768px) {
  .cta-section {
    padding: 80px 0;
    background-attachment: fixed;
  }
  
  .cta-section h2 {
    font-size: 2rem;
  }
  
  .cta-section p {
    font-size: 1rem;
    padding: 0 20px;
  }
  
  .cta-button {
    padding: 12px 25px;
    font-size: 1rem;
  }
}

@media (max-width: 480px) {
  .cta-section {
    padding: 60px 0;
  }
  
  .cta-section h2 {
    font-size: 1.8rem;
  }
  
  .cta-section p {
    font-size: 0.9rem;
  }
  
  .cta-button {
    padding: 10px 20px;
    font-size: 0.9rem;
  }
}
</style>