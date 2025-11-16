<template>
  <section class="categories" id="kategori">
    <div class="container">
      <div class="section-title">
        <h2>Jelajahi Kategori</h2>
        <p>Temukan berbagai jenis usaha lokal berdasarkan kategori favorit Anda</p>
      </div>

      <!-- Search Bar -->
      <div class="search-container">
        <div class="search-box">
          <i class="fas fa-search"></i>
          <input 
            type="text" 
            v-model="searchQuery" 
            placeholder="Cari UMKM berdasarkan nama..." 
            @input="handleSearch"
          >
          <button v-if="searchQuery" @click="clearSearch" class="clear-btn">
            <i class="fas fa-times"></i>
          </button>
        </div>
      </div>
      
      <!-- Daftar Kategori -->
      <div class="categories-grid">
        <div 
          class="category-card" 
          v-for="category in categories" 
          :key="category.id"
          @click="toggleCategory(category)"
          :class="{ 'active': selectedCategory?.id === category.id }"
        >
          <i :class="category.icon"></i>
          <h3>{{ category.name }}</h3>
          <span class="count">{{ category.count }} UMKM</span>
        </div>
      </div>

      <!-- Daftar UMKM berdasarkan kategori terpilih atau hasil search -->
      <div v-if="selectedCategory || searchQuery" class="umkm-section">
        <h3 class="umkm-title">
          {{ searchQuery ? `Hasil Pencarian "${searchQuery}"` : `UMKM ${selectedCategory.name}` }}
          <span class="result-count">({{ filteredUMKM.length }} hasil)</span>
        </h3>
        <div class="umkm-grid">
          <div 
            class="umkm-card" 
            v-for="umkm in filteredUMKM" 
            :key="umkm.id"
            @click="selectUMKM(umkm)"
          >
            <div class="umkm-image">
              <img :src="umkm.image" :alt="umkm.name" v-if="umkm.image">
              <div class="placeholder-image" v-else>
                <i class="fas fa-store"></i>
              </div>
              <span class="category-badge">{{ umkm.category }}</span>
            </div>
            <div class="umkm-info">
              <h4>{{ umkm.name }}</h4>
              <p class="umkm-description">{{ umkm.description }}</p>
              <div class="umkm-meta">
                <span class="rating">
                  <i class="fas fa-star"></i> {{ umkm.rating }}
                </span>
                <span class="distance">
                  <i class="fas fa-map-marker-alt"></i> {{ umkm.distance }}
                </span>
              </div>
              <div class="umkm-price" v-if="umkm.priceRange">
                {{ umkm.priceRange }}
              </div>
            </div>
          </div>
        </div>
        
        <!-- Pesan jika tidak ada hasil -->
        <div v-if="filteredUMKM.length === 0" class="no-results">
          <i class="fas fa-search"></i>
          <h4>Tidak ada UMKM yang ditemukan</h4>
          <p>Coba gunakan kata kunci lain atau pilih kategori yang berbeda</p>
        </div>
      </div>

      <!-- Modal Detail UMKM -->
      <div v-if="selectedUMKM" class="modal-overlay" @click="closeModal">
        <div class="modal-content" @click.stop>
          <button class="close-button" @click="closeModal">
            <i class="fas fa-times"></i>
          </button>
          
          <div class="modal-header">
            <div class="umkm-image-large">
              <img :src="selectedUMKM.image" :alt="selectedUMKM.name" v-if="selectedUMKM.image">
              <div class="placeholder-image-large" v-else>
                <i class="fas fa-store"></i>
              </div>
            </div>
            <div class="modal-title">
              <h2>{{ selectedUMKM.name }}</h2>
              <p class="umkm-category">{{ selectedUMKM.category }}</p>
              <div class="rating-large">
                <i class="fas fa-star"></i> {{ selectedUMKM.rating }}
                <span class="review-count">({{ selectedUMKM.reviews.length }} ulasan)</span>
              </div>
            </div>
          </div>

          <div class="modal-body">
            <div class="info-section">
              <h4>Deskripsi</h4>
              <p>{{ selectedUMKM.fullDescription }}</p>
            </div>

            <div class="info-section" v-if="selectedUMKM.products && selectedUMKM.products.length">
              <h4>Menu & Layanan</h4>
              <div class="products-grid">
                <div 
                  class="product-card" 
                  v-for="product in selectedUMKM.products" 
                  :key="product.id"
                  @click="addToOrder(product)"
                >
                  <div class="product-info">
                    <h5>{{ product.name }}</h5>
                    <p class="product-description">{{ product.description }}</p>
                    <span class="product-price">Rp {{ formatPrice(product.price) }}</span>
                  </div>
                  <button class="add-button">
                    <i class="fas fa-plus"></i>
                  </button>
                </div>
              </div>
            </div>

            <div class="info-section">
              <h4>Informasi</h4>
              <div class="info-grid">
                <div class="info-item">
                  <i class="fas fa-clock"></i>
                  <span>{{ selectedUMKM.operationalHours }}</span>
                </div>
                <div class="info-item">
                <i class="fas fa-map-marker-alt"></i>
                <a 
                  :href="selectedUMKM.mapsUrl" 
                  target="_blank"
                  class="address-link"
                >
                  {{ selectedUMKM.address }}
                  <i class="fas fa-external-link-alt external-icon"></i>
                </a>
              </div>
                <div class="info-item">
                  <i class="fas fa-phone"></i>
                  <span>{{ selectedUMKM.phone }}</span>
                </div>
              </div>
            </div>

            <!-- Order Summary -->
            <div v-if="orderItems.length > 0" class="order-section">
              <h4>Pesanan Anda</h4>
              <div class="order-items">
                <div 
                  class="order-item" 
                  v-for="(item, index) in orderItems" 
                  :key="index"
                >
                  <span class="item-name">{{ item.name }}</span>
                  <div class="item-controls">
                    <button @click="decreaseQuantity(index)" class="quantity-btn">
                      <i class="fas fa-minus"></i>
                    </button>
                    <span class="quantity">{{ item.quantity }}</span>
                    <button @click="increaseQuantity(index)" class="quantity-btn">
                      <i class="fas fa-plus"></i>
                    </button>
                  </div>
                  <span class="item-total">Rp {{ formatPrice(item.total) }}</span>
                  <button @click="removeFromOrder(index)" class="remove-btn">
                    <i class="fas fa-trash"></i>
                  </button>
                </div>
              </div>
              <div class="order-total">
                <strong>Total: Rp {{ formatPrice(totalOrder) }}</strong>
              </div>
              <button class="order-button" @click="processOrder">
                <i class="fas fa-shopping-cart"></i>
                Pesan Sekarang
              </button>
            </div>

            <!-- Reviews Section -->
            <div class="info-section">
              <div class="reviews-header">
                <h4>Ulasan & Rating ({{ selectedUMKM.reviews.length }})</h4>
                <button class="add-review-btn" @click="showReviewForm = true">
                  <i class="fas fa-plus"></i> Tambah Ulasan
                </button>
              </div>

              <!-- Review Form -->
              <div v-if="showReviewForm" class="review-form">
                <h5>Tulis Ulasan Anda</h5>
                <div class="rating-input">
                  <span>Rating:</span>
                  <div class="star-rating">
                    <span 
                      v-for="star in 5" 
                      :key="star"
                      class="star"
                      :class="{ 'filled': newReview.rating >= star }"
                      @click="setRating(star)"
                    >
                      <i class="fas fa-star"></i>
                    </span>
                  </div>
                </div>
                <textarea 
                  v-model="newReview.comment" 
                  placeholder="Bagaimana pengalaman Anda?"
                  rows="4"
                ></textarea>
                <input 
                  type="text" 
                  v-model="newReview.author" 
                  placeholder="Nama Anda (opsional)"
                >
                <div class="review-actions">
                  <button @click="submitReview" class="submit-review-btn">Kirim Ulasan</button>
                  <button @click="cancelReview" class="cancel-review-btn">Batal</button>
                </div>
              </div>

              <!-- Reviews List -->
              <div class="reviews-list">
                <div 
                  class="review-item" 
                  v-for="review in selectedUMKM.reviews" 
                  :key="review.id"
                >
                  <div class="review-header">
                    <div class="reviewer-info">
                      <div class="reviewer-avatar">
                        {{ getInitials(review.author) }}
                      </div>
                      <div>
                        <strong>{{ review.author || 'Anonim' }}</strong>
                        <div class="review-stars">
                          <span 
                            v-for="star in 5" 
                            :key="star"
                            class="star small"
                            :class="{ 'filled': review.rating >= star }"
                          >
                            <i class="fas fa-star"></i>
                          </span>
                        </div>
                      </div>
                    </div>
                    <span class="review-date">{{ review.date }}</span>
                  </div>
                  <p class="review-comment">{{ review.comment }}</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: 'Categories',
  data() {
    return {
      searchQuery: '',
      selectedCategory: null,
      selectedUMKM: null,
      orderItems: [],
      showReviewForm: false,
      newReview: {
        rating: 0,
        comment: '',
        author: ''
      },
      categories: [
        { id: 1, name: 'Kuliner', icon: 'fas fa-utensils', count: 7 },
        { id: 2, name: 'Jasa', icon: 'fas fa-tools', count: 3 },
        { id: 3, name: 'Fashion', icon: 'fas fa-tshirt', count: 0 },
        { id: 4, name: 'Kesehatan', icon: 'fas fa-heart', count: 0 },
      ],
      
      umkmData: [
        {
          id: 1,
          name: 'Dian Kebab',
          category: 'Kuliner',
          image: '/dian-kebab.png',
          description: 'Kebab Favorit Mahasiswa UPNVJ',
          fullDescription: 'Nikmati berbagai varian Kebab dengan daging yang dibumbui sempurna dan dibakar perlahan, dipadukan dengan sayuran segar dan saus khas rahasia.',
          rating: 4.8,
          reviews: [
            {
              id: 1,
              author: 'Ali Mahdi M',
              rating: 5,
              comment: 'Kebabnya enak banget! Pelayanan juga ramah dan cepat.',
              date: '2 hari yang lalu'
            },
            {
              id: 2,
              author: 'Haikal Sulaeman',
              rating: 4,
              comment: 'Kebab jumbonya bukan main, bikin aku ketagihan dan meleleh.',
              date: '1 minggu yang lalu'
            }
          ],
          priceRange: 'Rp 9.000 - Rp 17.000',
          operationalHours: '12:00 - 23:59',
          address: 'Jl. Pangkalan Jati 1 No.3, Pangkalan Jati, Kec. Cinere, Kota Depok, Jawa Barat 16513',
          phone: '0857 1308 6618',
          mapsUrl: 'https://maps.app.goo.gl/uLHYcjthEi7N3Bee8',
          products: [
            { id: 1, name: 'Kebab Mini', description: 'Regular puas mantap', price: 9000 },
            { id: 2, name: 'Kebab Sedang', description: 'Dengan lebih banyak isian', price: 11000 },
            { id: 3, name: 'Kebab Besar', description: 'Puas dan kenyang', price: 13000 },
            { id: 4, name: 'Kebab Jumbo', description: 'Isian ayam dan sayuran lebi banyak', price: 17000 }
          ]
        },
        {
          id: 2,
          name: 'Ombe Love',
          category: 'Kuliner',
          image: '/ombelove.png',
          description: 'Minuman dengan aneka rasa menyegarkan',
          fullDescription: 'Nikmati berbagai racikan es unik dari bahan-bahan pilihan terbaik, menghasilkan rasa yang manis, segar, dan menghilangkan dahaga.',
          rating: 4.9,
          reviews: [
            {
              id: 1,
              author: 'Vicky Surya W',
              rating: 5,
              comment: 'Es terenak yang pernah saya coba! Kesegarannya bikin ketagihan!🧊😋',
              date: '3 hari yang lalu'
            }
          ],
          priceRange: 'Rp 3.000 - Rp 15.000',
          operationalHours: '10:00 - 21:00',
          address: 'Jl. Pangkalan Jati 1 No.3, Pangkalan Jati, Kec. Cinere, Kota Depok, Jawa Barat 16513',
          phone: '0852 2074 4533',
          mapsUrl: 'https://maps.app.goo.gl/uLHYcjthEi7N3Bee8',
          products: [
            { "id": 5, "name": "Es Teh Manis", "description": "Teh pilihan dengan gula asli", "price": 3000 },
            { "id": 6, "name": "Es Jeruk Peras", "description": "Jeruk peras murni segar", "price": 5000 },
            { "id": 7, "name": "Durian Tea", "description": "Teh dengan ekstrak durian", "price": 10000 },
            { "id": 8, "name": "Kopi Susu Gula Aren", "description": "Espresso dengan susu dan gula aren", "price": 15000 }
          ]
        },
        {
          id: 3,
          name: 'Jhon Crepes',
          category: 'Kuliner',
          image: '/jhon.png',
          description: 'Crepes renyah dengan isian manis atau gurih',
          fullDescription: 'Crepes tipis dan lembut yang dibuat dengan resep otentik. Pilihan isian beragam dari cokelat, keju, buah segar, hingga daging asap.',
          rating: 4.9,
          reviews: [
            {
              id: 1,
              author: 'Rayhan',
              rating: 5,
              comment: 'Crepes terenak yang pernah saya coba! Kriuknya bikin ketagihan! 🥞😋',
              date: '3 hari yang lalu'
            }
          ],
          priceRange: 'Rp 5000 - Rp 10.000',
          operationalHours: '08:00 - 16:00',
          address: 'Jl. Pangkalan Jati No.1a, RT.1/RW.1, Pangkalan Jati, Kec. Cinere, Kota Depok, Jawa Barat 16513',
          phone: '0815 8579 5896',
          mapsUrl: 'https://maps.app.goo.gl/1rtW4HdyVPALBtzX8',
          products: [
            { "id": 9, "name": "Crepes Coklat Keju", "description": "Crepes renyah dengan lelehan cokelat dan taburan keju", "price": 5000 },
            { "id": 10, "name": "Crepes Tiramisu Kacang", "description": "Crepes dengan krim tiramisu dan topping kacang renyah", "price": 7000 },
            { "id": 11, "name": "Crepes Pisang Coklat Keju", "description": "Isian pisang, cokelat, dan keju yang manis gurih", "price": 8000 },
            { "id": 12, "name": "Crepes Pisang Keju Susu Coklat", "description": "Isian pisang, keju, susu kental, dan saus cokelat", "price": 10000 }
          ]
        },
        {
          id: 4,
          name: 'Mie Ayam & Bakso Binangun',
          category: 'Kuliner',
          image: '/mieayam.png',
          description: 'Mie ayam dengan toping ayam gurih dan bakso kenyal',
          fullDescription: 'Paduan sempurna mie yang kenyal, toping ayam berbumbu legendaris, dan bakso daging sapi pilihan dengan kuah kaldu yang gurih dan sedap.',
          rating: 4.9,
          reviews: [
            {
              id: 1,
              author: 'Samuel Aditya',
              rating: 5,
              comment: 'Bakso terenak yang pernah saya coba! Kuahnya bikin ketagihan.',
              date: '7 hari yang lalu'
            }
          ],
          priceRange: 'Rp 13.000 - Rp 20.000',
          operationalHours: '10:00 - 18:30',
          address: 'Jl. Pangkalan Jati 1 No.26g, Pd. Labu, Kec. Cilandak, Kota Jakarta Selatan, Daerah Khusus Ibukota Jakarta',
          phone: '0831 0519 7231',
          mapsUrl: 'hhttps://maps.app.goo.gl/sUaPNFyMdbgjxyXx7',
          products: [
            { id: 13, name: 'Bakso Special', description: '10 butir bakso + mie', price: 13000 },
            { id: 14, name: 'Bakso Urat', description: 'Bakso dengan urat sapi', price: 13000 },
            { id: 15, name: 'Es Teh Manis', description: 'Dengan gula aren', price: 16000 },
            { id: 16, name: 'Kerupuk', description: 'Kerupuk kulit', price: 20000 }
          ]
        },
        {
          id: 5,
          name: 'Geprek Mavera',
          category: 'Kuliner',
          image: '/mavera.png',
          description: 'Ayam crispy yang digeprek dengan sambal level suka-suka',
          fullDescription: 'Geprek Mavera, Ayam Goreng Crispy legendaris yang digeprek dengan sambal khas berlimpah, hadir dengan berbagai pilihan topping seperti keju mozzarella atau nori.',
          rating: 4.9,
          reviews: [
            {
              id: 1,
              author: 'Dika Pramana',
              rating: 5,
              comment: 'Rasanya enak bgt pls, siapapun beliin aku 3',
              date: '5 hari yang lalu'
            }
          ],
          priceRange: 'Rp 15.000 - Rp 20.000',
          operationalHours: '10:00 - 22:00',
          address: '14 Komp. DDN II, Jl. Pangkalan Jati No.26, RT.14/RW.1, Pd. Labu, Kec. Cilandak, Kota Jakarta Selatan, Daerah Khusus Ibukota Jakarta 12450',
          phone: '0856 8044 910',
          mapsUrl: 'https://maps.app.goo.gl/CQLc1RgEGpWvi1aW9',
          products: [
            { 'id': 17, 'name': 'Ayam Geprek', 'description': 'Ayam crispy digeprek dengan sambal khas', 'price': 15000 },
            { 'id': 18, 'name': 'Ayam Geprek Keju', 'description': 'Ayam geprek dengan parutan keju cheddar', 'price': 18000 },
            { 'id': 19, 'name': 'Ayam Geprek Mentai', 'description': 'Ayam geprek dengan saus mentai gurih dibakar', 'price': 19000 },
            { 'id': 20, 'name': 'Ayam Geprek Moza', 'description': 'Ayam geprek dengan lelehan keju mozzarella', 'price': 20000 }
          ]
        },
        {
          id: 6,
          name: 'Jus Gaduh Rasa',
          category: 'Kuliner',
          image: '/jus.png',
          description: 'Jus buah segar pilihan yang menyegarkan',
          fullDescription: 'Jus buah asli, dibuat dari buah segar tanpa tambahan gula atau pemanis buatan. Kaya vitamin dan menyehatkan.',
          rating: 4.9,
          reviews: [
            {
              id: 1,
              author: 'Sunyi Sarah',
              rating: 5,
              comment: 'Jus nya enak bgt, ga encerr. debest daha',
              date: '1 hari yang lalu'
            }
          ],
          priceRange: 'Rp 10.000 - Rp 20.000',
          operationalHours: '08:00 - 23:59',
          address: 'Jl. Pangkalan Jati 2 No.1, Pangkalan Jati, Kec. Cinere, Kota Depok, Jawa Barat 16514',
          phone: '0858 1408 6205',
          mapsUrl: 'https://maps.app.goo.gl/GwVx5mpTiG4cNFHX9',
          products: [
            { 'id': 21, 'name': 'Jus Regular Semua Rasa', 'description': 'Pilih satu rasa buah (Mangga, Jeruk, Jambu, dll)', 'price': 10000 },
            { 'id': 22, 'name': 'Jus Mix 2 Rasa', 'description': 'Kombinasi dua jenis buah pilihan (contoh: Alpukat & Cokelat)', 'price': 12000 },
            { 'id': 23, 'name': 'Jus Mix 3 Rasa', 'description': 'Kombinasi tiga jenis buah pilihan (contoh: Naga, Pisang, & Stroberi)', 'price': 15000 }
          ]
        },
        {
          id: 7,
          name: 'Batagor & Siomay Mang Ifan',
          category: 'Kuliner',
          image: '/batagor.png',
          description: 'Batagor dan Siomay khas Bandung dengan bumbu kacang istimewa',
          fullDescription: 'Batagor dan Siomay dibuat dari ikan tenggiri segar. Disajikan dengan bumbu kacang kental, gurih, dan sedikit pedas.',
          rating: 4.9,
          mapsUrl: 'https://maps.app.goo.gl/GwVx5mpTiG4cNFHX9',
          reviews: [
            {
              id: 1,
              author: 'Zahra safitri',
              rating: 5,
              comment: 'Batagornya gede gedek bgt plis. nagih deh',
              date: '3 hari yang lalu'
            }
          ],
          priceRange: 'Rp 5.000 - Rp 20.000',
          operationalHours: '08:00 - 23:59',
          address: 'Jl. Pangkalan Jati 2 No.1, Pangkalan Jati, Kec. Cinere, Kota Depok, Jawa Barat 16514',
          phone: '0852 2499 0734',
          products: [
            { id: 24, name: 'Batagor Porsi Kecil', description: '3 potong Batagor dan bumbu kacang', price: 5000 },
            { id: 25, name: 'Batagor Porsi Sedang', description: '5 potong Batagor dan bumbu kacang', price: 10000 },
            { id: 26, name: 'Batagor Porsi Besar', description: '7 potong Batagor dan bumbu kacang', price: 15000 },
            { id: 27, name: 'Batagor Porsi Jumbo', description: '10 potong Batagor dan bumbu kacang', price: 20000 }
          ]
        },
        {
          id: 8,
          name: 'Bengkel Berkah Jaya Motor',
          category: 'Jasa',
          image: '/bengkel.png',
          description: 'Menangani segala jenis perbaikan dan perawatan kendaraan Anda',
          fullDescription: 'Ditangani oleh teknisi bersertifikat dan berpengalaman yang siap memberikan diagnosa akurat dan solusi terbaik.',
          rating: 4.7,
          reviews: [
            {
              id: 1,
              author: 'Bismar Sinaga',
              rating: 5,
              comment: 'Rekomen ni gais, saya habis bore up disini, biayanya ga mahal',
              date: '2 hari yang lalu'
            }
          ],
          priceRange: 'Rp 50.000 - Rp 150.000',
          operationalHours: '08:00 - 17:30',
          address: 'Jl. Pangkalan Jati 1 No.3, Pangkalan Jati, Kec. Cinere, Kota Depok, Jawa Barat 16513',
          phone: '0823 1133 6734',
          mapsUrl: 'https://maps.app.goo.gl/FUnr8z8xH3qEUFR59',
          products: [
            { "id": 28, "name": "Pasang Onderdil", "description": "Jasa pemasangan suku cadang/onderdil", "price": 50000 },
            { "id": 29, "name": "Cek Mesin", "description": "Jasa pemeriksaan dan diagnosa mesin kendaraan", "price": 100000 },
            { "id": 30, "name": "Cek Kendaraan Bekas", "description": "Jasa pemeriksaan menyeluruh kendaraan bekas sebelum pembelian", "price": 150000 }
          ]
        },
        {
          id: 9,
          name: 'BUNGLONDRY',
          category: 'Jasa',
          image: '/laundry.png',
          description: 'Jasa laundry (binatu) profesional dan cepat',
          fullDescription: 'Layanan laundry kiloan dan satuan. Menggunakan deterjen premium, mesin modern, dan proses higienis. Garansi bersih, wangi, dan rapi.',
          rating: 4.7,
          reviews: [
            {
              id: 1,
              author: 'Satria Hitam',
              rating: 5,
              comment: 'disini per kilo murah bat giluyyy',
              date: '5 hari yang lalu'
            }
          ],
          priceRange: 'Rp 7.500 - Rp 40.000',
          operationalHours: '09:00 - 21:00',
          address: 'Jl. Pangkalan Jati 1 No.3, Pangkalan Jati, Kec. Cinere, Kota Depok, Jawa Barat 16513',
          phone: '0813 1862 7043',
          mapsUrl: 'https://maps.app.goo.gl/FMap7Ud2YN7VC8Uw7',
          products: [
            { id: 31, name: 'Laundry Kiloan', description: 'Cuci, kering, dan setrika untuk pakaian harian (per kg)', price: 7500 },
            { id: 32, name: 'Cuci Sepatu', description: 'Perawatan dan pembersihan sepatu segala jenis', price: 40000 }
          ]
        },
        {
          id: 10,
          name: 'Arinda Komputer',
          category: 'Jasa',
          image: '/arinda.png',
          description: 'Pusat layanan perbaikan dan pemeliharaan laptop profesional',
          fullDescription: 'Menerima service segala kerusakan hardware dan software. Teknisi bersertifikat, pengerjaan cepat, dan bergaransi resmi.',
          rating: 4.7,
          reviews: [
            {
              id: 1,
              author: 'Bumi Sinaga',
              rating: 5,
              comment: 'Saya habis nambah ram disini, harganya terjangkau bgt',
              date: '5 hari yang lalu'
            }
          ],
          priceRange: 'Rp 30.000 - Rp 100.000',
          operationalHours: '09:00 - 17:00',
          address: '14 Jalan Pangkalan Jati Raya 1, RT.14/RW.1, Pd. Labu, Kec. Cilandak, Kota Jakarta Selatan, Daerah Khusus Ibukota Jakarta 12450',
          phone: '0821 7507 2402',
          mapsUrl: 'https://maps.app.goo.gl/Va9FZ7zf3C2HJkha6',
          products: [
            { id: 34, name: 'Jasa Inspeksi', description: 'Diagnosa dan pemeriksaan kerusakan laptop', price: 30000 },
            { id: 35, name: 'Ganti Pasta & Bersihkan Laptop', description: 'Penggantian thermal paste dan pembersihan debu internal', price: 50000 },
            { id: 36, name: 'Install Ulang Windows', description: 'Instalasi sistem operasi (OS) dan driver', price: 150000 }
          ]
        },
      ]
    }
  },
  computed: {
    filteredUMKM() {
      if (this.searchQuery) {
        const query = this.searchQuery.toLowerCase();
        return this.umkmData.filter(umkm => 
          umkm.name.toLowerCase().includes(query) ||
          umkm.description.toLowerCase().includes(query) ||
          umkm.category.toLowerCase().includes(query)
        );
      }
      
      if (!this.selectedCategory) return []
      return this.umkmData.filter(umkm => umkm.category === this.selectedCategory.name)
    },
    totalOrder() {
      return this.orderItems.reduce((total, item) => total + item.total, 0)
    }
  },
  methods: {
    toggleCategory(category) {
      if (this.selectedCategory?.id === category.id) {
        this.selectedCategory = null;
      } else {
        this.selectedCategory = category;
        this.searchQuery = '';
      }
      this.selectedUMKM = null;
      this.orderItems = [];
    },
    
    handleSearch() {
      if (this.searchQuery) {
        this.selectedCategory = null;
      }
    },
    
    clearSearch() {
      this.searchQuery = '';
    },
    
    selectUMKM(umkm) {
      this.selectedUMKM = umkm;
      this.orderItems = [];
      this.showReviewForm = false;
      this.newReview = {
        rating: 0,
        comment: '',
        author: ''
      };
    },
    
    closeModal() {
      this.selectedUMKM = null;
      this.orderItems = [];
      this.showReviewForm = false;
      this.newReview = {
        rating: 0,
        comment: '',
        author: ''
      };
    },
    
    addToOrder(product) {
      const existingItem = this.orderItems.find(item => item.id === product.id)
      
      if (existingItem) {
        existingItem.quantity++
        existingItem.total = existingItem.quantity * product.price
      } else {
        this.orderItems.push({
          id: product.id,
          name: product.name,
          price: product.price,
          quantity: 1,
          total: product.price
        })
      }
    },
    
    increaseQuantity(index) {
      this.orderItems[index].quantity++
      this.orderItems[index].total = this.orderItems[index].quantity * this.orderItems[index].price
    },
    
    decreaseQuantity(index) {
      if (this.orderItems[index].quantity > 1) {
        this.orderItems[index].quantity--
        this.orderItems[index].total = this.orderItems[index].quantity * this.orderItems[index].price
      }
    },
    
    removeFromOrder(index) {
      this.orderItems.splice(index, 1)
    },
    
    formatPrice(price) {
      return price.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".")
    },
    
    processOrder() {
      if (this.orderItems.length === 0) {
        alert('Silakan pilih produk terlebih dahulu!')
        return
      }
      
      const orderDetails = {
        umkm: this.selectedUMKM.name,
        items: this.orderItems,
        total: this.totalOrder,
        timestamp: new Date().toLocaleString()
      }
      
      alert(`Pesanan berhasil!\n\nUMKM: ${orderDetails.umkm}\nTotal: Rp ${this.formatPrice(orderDetails.total)}\n\nTerima kasih telah berbelanja!`)
      
      this.orderItems = []
      this.selectedUMKM = null
    },
    
    setRating(rating) {
      this.newReview.rating = rating
    },
    
    submitReview() {
      if (this.newReview.rating === 0) {
        alert('Silakan berikan rating terlebih dahulu!')
        return
      }

      if (!this.newReview.comment.trim()) {
        alert('Silakan tulis ulasan Anda!')
        return
      }

      const newReview = {
        id: Date.now(),
        author: this.newReview.author || 'Anonim',
        rating: this.newReview.rating,
        comment: this.newReview.comment,
        date: 'Baru saja'
      }

      this.selectedUMKM.reviews.unshift(newReview)
      this.updateUMKMRating()
      this.cancelReview()
      alert('Terima kasih! Ulasan Anda telah ditambahkan.')
    },
    
    cancelReview() {
      this.showReviewForm = false
      this.newReview = {
        rating: 0,
        comment: '',
        author: ''
      }
    },
    
    updateUMKMRating() {
      if (this.selectedUMKM.reviews.length > 0) {
        const totalRating = this.selectedUMKM.reviews.reduce((sum, review) => sum + review.rating, 0)
        this.selectedUMKM.rating = (totalRating / this.selectedUMKM.reviews.length).toFixed(1)
      }
    },
    
    getInitials(name) {
      if (!name) return 'A'
      return name.split(' ').map(n => n[0]).join('').toUpperCase().substring(0, 2)
    }
  }
}
</script>

<style scoped>
.categories {
  padding: 100px 0;
  background: white ;
  background-size: cover;
}

/* Search Bar Styles */
.search-container {
  max-width: 600px;
  margin: 0 auto 40px auto;
}

.search-box {
  position: relative;
  display: flex;
  align-items: center;
  background: white;
  border-radius: 50px;
  padding: 12px 20px;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.08);
  border: 2px solid transparent;
  transition: all 0.3s ease;
}

.search-box:focus-within {
  border-color: var(--primary);
  box-shadow: 0 5px 20px rgba(52, 152, 219, 0.2);
}

.search-box i.fa-search {
  color: #666;
  margin-right: 10px;
  font-size: 1.1rem;
}

.search-box input {
  flex: 1;
  border: none;
  outline: none;
  font-size: 1rem;
  background: transparent;
  color: #333;
}

.search-box input::placeholder {
  color: #999;
}

.clear-btn {
  background: none;
  border: none;
  color: #666;
  cursor: pointer;
  padding: 5px;
  border-radius: 50%;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
}

.clear-btn:hover {
  background: #f0f0f0;
  color: #333;
}

.result-count {
  font-size: 1rem;
  color: #666;
  font-weight: normal;
  margin-left: 10px;
}

.no-results {
  text-align: center;
  padding: 60px 20px;
  color: #666;
}

.no-results i {
  font-size: 4rem;
  color: #ddd;
  margin-bottom: 20px;
}

.no-results h4 {
  font-size: 1.5rem;
  margin-bottom: 10px;
  color: #333;
}

.no-results p {
  font-size: 1rem;
  color: #666;
}

/* Categories Grid */
.categories-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 20px;
  margin-bottom: 50px;
}

.category-card {
  background: white;
  border-radius: 15px;
  padding: 30px 20px;
  text-align: center;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.08);
  transition: all 0.3s ease;
  cursor: pointer;
  border: 3px solid transparent;
}

.category-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
}

.category-card.active {
  border-color: var(--primary);
  background: var(--gradient);
  color: white;
}

.category-card.active i {
  color: white;
}

.category-card i {
  font-size: 2.5rem;
  margin-bottom: 15px;
  color: var(--primary);
  transition: color 0.3s;
}

.count {
  display: block;
  margin-top: 10px;
  font-size: 0.9rem;
  opacity: 0.7;
}

/* UMKM Section */
.umkm-section {
  margin-top: 50px;
}

.umkm-image img,
.umkm-image-large img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: inherit;
}

.umkm-title {
  font-size: 2rem;
  color: var(--dark);
  margin-bottom: 30px;
  text-align: center;
}

.umkm-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 25px;
}

.umkm-card {
  background: white;
  border-radius: 15px;
  overflow: hidden;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.08);
  transition: all 0.3s ease;
  cursor: pointer;
}

.umkm-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
}

.umkm-image {
  position: relative;
  height: 180px;
  background: var(--gradient);
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
}

.placeholder-image {
  font-size: 3rem;
}

.placeholder-image-large {
  font-size: 4rem;
}

.category-badge {
  position: absolute;
  top: 15px;
  right: 15px;
  background: rgba(255, 255, 255, 0.9);
  padding: 5px 10px;
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: 600;
  color: var(--dark);
}

.umkm-info {
  padding: 20px;
}

.umkm-info h4 {
  font-size: 1.3rem;
  margin-bottom: 10px;
  color: var(--dark);
}

.umkm-description {
  color: #666;
  margin-bottom: 15px;
  font-size: 0.9rem;
}

.umkm-meta {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
  font-size: 0.9rem;
}

.rating, .distance {
  display: flex;
  align-items: center;
  gap: 5px;
}

.rating {
  color: #FFD700;
}

.distance {
  color: #666;
}

.umkm-price {
  font-weight: 600;
  color: var(--primary);
}

/* Modal Styles */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  padding: 20px;
}

.modal-content {
  background: white;
  border-radius: 20px;
  max-width: 800px;
  width: 100%;
  max-height: 90vh;
  overflow-y: auto;
  position: relative;
}

.close-button {
  position: absolute;
  top: 20px;
  right: 20px;
  background: #ff4444;
  color: white;
  border: none;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  z-index: 1001;
  font-size: 1.2rem;
  font-weight: bold;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
  transition: all 0.3s ease;
}

.close-button:hover {
  background: #ff0000;
  transform: scale(1.1);
}

.modal-header {
  display: flex;
  gap: 20px;
  padding: 30px;
  border-bottom: 1px solid #eee;
}

.umkm-image-large {
  width: 120px;
  height: 120px;
  background: var(--gradient);
  border-radius: 15px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  flex-shrink: 0;
}

.modal-title {
  flex: 1;
}

.modal-title h2 {
  font-size: 1.8rem;
  margin-bottom: 5px;
  color: var(--dark);
}

.umkm-category {
  color: var(--primary);
  font-weight: 600;
  margin-bottom: 10px;
}

.rating-large {
  display: flex;
  align-items: center;
  gap: 5px;
  color: #FFD700;
  font-weight: 600;
}

.review-count {
  color: #666;
  font-weight: normal;
}

.modal-body {
  padding: 30px;
}

.info-section {
  margin-bottom: 30px;
}

.info-section h4 {
  font-size: 1.3rem;
  margin-bottom: 15px;
  color: var(--dark);
}

.products-grid {
  display: grid;
  gap: 15px;
}

.product-card {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px;
  background: #f8f9fa;
  border-radius: 10px;
  cursor: pointer;
  transition: background 0.3s;
}

.product-card:hover {
  background: #e9ecef;
}

.product-info h5 {
  margin-bottom: 5px;
  color: var(--dark);
}

.product-description {
  color: #666;
  font-size: 0.9rem;
  margin-bottom: 5px;
}

.product-price {
  font-weight: 600;
  color: var(--primary);
}

.add-button {
  background: var(--primary);
  color: white;
  border: none;
  border-radius: 50%;
  width: 35px;
  height: 35px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}

.info-grid {
  display: grid;
  gap: 10px;
}

.info-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 0;
}

.info-item i {
  color: var(--primary);
  width: 20px;
}

/* Order Section */
.order-section {
  background: #f8f9fa;
  padding: 20px;
  border-radius: 10px;
  margin-top: 30px;
}

.order-items {
  margin-bottom: 20px;
}

.order-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 0;
  border-bottom: 1px solid #dee2e6;
}

.item-controls {
  display: flex;
  align-items: center;
  gap: 10px;
}

.quantity-btn {
  background: var(--primary);
  color: white;
  border: none;
  border-radius: 5px;
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}

.remove-btn {
  background: #dc3545;
  color: white;
  border: none;
  border-radius: 5px;
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}

.order-total {
  text-align: right;
  font-size: 1.2rem;
  margin-bottom: 15px;
  color: var(--dark);
}

.order-button {
  width: 100%;
  background: var(--gradient);
  color: white;
  border: none;
  padding: 15px;
  border-radius: 10px;
  font-size: 1.1rem;
  font-weight: 600;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  transition: transform 0.3s;
}

.order-button:hover {
  transform: translateY(-2px);
}

/* Reviews Section */
.reviews-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.add-review-btn {
  background: var(--primary);
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 20px;
  cursor: pointer;
  font-size: 0.9rem;
  display: flex;
  align-items: center;
  gap: 5px;
  transition: background 0.3s;
}

.add-review-btn:hover {
  background: #e55a5a;
}

.review-form {
  background: #f8f9fa;
  padding: 20px;
  border-radius: 10px;
  margin-bottom: 20px;
}

.review-form h5 {
  margin-bottom: 15px;
  color: var(--dark);
}

.rating-input {
  margin-bottom: 15px;
}

.rating-input span {
  display: block;
  margin-bottom: 8px;
  font-weight: 500;
}

.star-rating {
  display: flex;
  gap: 5px;
}

.star {
  font-size: 1.5rem;
  color: #ddd;
  cursor: pointer;
  transition: color 0.2s;
}

.star.filled {
  color: #FFD700;
}

.star:hover {
  color: #FFD700;
}

.star.small {
  font-size: 0.9rem;
}

.review-form textarea,
.review-form input {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
  margin-bottom: 10px;
  font-family: inherit;
}

.review-form textarea {
  resize: vertical;
}

.review-actions {
  display: flex;
  gap: 10px;
}

.submit-review-btn {
  background: var(--primary);
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
  flex: 1;
}

.cancel-review-btn {
  background: #6c757d;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
}

.reviews-list {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.review-item {
  background: white;
  padding: 15px;
  border-radius: 8px;
  border: 1px solid #eee;
}

.review-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 10px;
}

.reviewer-info {
  display: flex;
  align-items: center;
  gap: 10px;
}

.reviewer-avatar {
  width: 40px;
  height: 40px;
  background: var(--gradient);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-weight: bold;
  font-size: 0.9rem;
}

.review-stars {
  display: flex;
  gap: 2px;
  margin-top: 2px;
}

.review-date {
  color: #666;
  font-size: 0.8rem;
}

.review-comment {
  color: #333;
  line-height: 1.5;
}

.address-link {
  color: inherit;
  text-decoration: none;
  transition: color 0.3s ease;
  cursor: pointer;
}

.address-link:hover {
  color: var(--primary);
  text-decoration: underline;
}

/* Responsive */
@media (max-width: 768px) {
  .search-container {
    margin: 0 auto 30px auto;
    padding: 0 15px;
  }
  
  .search-box {
    padding: 10px 15px;
  }
  
  .search-box input {
    font-size: 0.9rem;
  }
  
  .categories-grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 15px;
  }
  
  .umkm-grid {
    grid-template-columns: 1fr;
  }
  
  .umkm-title {
    font-size: 1.5rem;
  }
  
  .result-count {
    display: block;
    margin-left: 0;
    margin-top: 5px;
  }
  
  .modal-header {
    flex-direction: column;
    text-align: center;
  }
  
  .umkm-image-large {
    align-self: center;
  }
  
  .modal-content {
    margin: 10px;
  }
  
  .reviews-header {
    flex-direction: column;
    gap: 15px;
    align-items: flex-start;
  }
  
  .review-header {
    flex-direction: column;
    gap: 10px;
  }
  
  .review-actions {
    flex-direction: column;
  }
}

@media (max-width: 480px) {
  .search-box {
    border-radius: 25px;
  }
  
  .categories-grid {
    grid-template-columns: 1fr;
  }
}
</style>