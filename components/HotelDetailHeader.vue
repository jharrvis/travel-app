<template>
  <div class="hotel-detail-header">
    <!-- Main Gallery Section -->
    <div class="gallery-container">
      <!-- Main Image Section (Left) -->
      <div class="main-image-container">
        <div class="image-wrapper">
          <img
            :src="currentMainImage"
            :alt="hotelData.name"
            class="main-image"
            @click="openGalleryModal(currentMainImageIndex)"
          />

          <!-- Gallery Navigation Overlay - Displayed only on hover for primary navigation -->
          <div class="gallery-nav-overlay">
            <button
              v-if="hasMultipleImages"
              class="nav-arrow nav-arrow-left"
              @click.stop="previousMainImage"
            >
              <svg width="24" height="24" viewBox="0 0 24 24" fill="none">
                <path
                  d="M15 18L9 12L15 6"
                  stroke="white"
                  stroke-width="2"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                />
              </svg>
            </button>

            <button
              v-if="hasMultipleImages"
              class="nav-arrow nav-arrow-right"
              @click.stop="nextMainImage"
            >
              <svg width="24" height="24" viewBox="0 0 24 24" fill="none">
                <path
                  d="M9 18L15 12L9 6"
                  stroke="white"
                  stroke-width="2"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                />
              </svg>
            </button>
          </div>

          <!-- Image Counter -->
          <div class="image-counter">
            {{ currentMainImageIndex + 1 }} / {{ totalImages }}
          </div>

          <!-- View All Photos Button -->
          <button class="view-all-photos-btn" @click="openGalleryModal(0)">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
              <rect
                x="3"
                y="3"
                width="18"
                height="18"
                rx="2"
                ry="2"
                stroke="white"
                stroke-width="2"
              />
              <circle cx="8.5" cy="8.5" r="1.5" fill="white" />
              <polyline
                points="21,15 16,10 5,21"
                stroke="white"
                stroke-width="2"
              />
            </svg>
            View all photos ({{ totalImages }})
          </button>
        </div>
      </div>

      <!-- Right Column - 2 Fixed Thumbnails Only -->
      <div class="side-thumbnails-wrapper">
        <!-- Fixed 2 Thumbnails -->
        <div class="visible-thumbnails-container">
          <div
            v-for="(image, index) in fixedThumbnails"
            :key="'fixed-' + index"
            class="thumbnail-item"
            :class="{ active: index + 1 === currentMainImageIndex }"
            @click="setMainImage(index + 1)"
          >
            <img :src="image" :alt="`${hotelData.name} view ${index + 2}`" />
          </div>
        </div>
      </div>
    </div>

    <!-- Hotel Information Section -->
    <div class="hotel-info-section">
      <div class="hotel-header-meta">
        <!-- Hotel Rating - Using '★' character and styling from HotelCard -->
        <div class="hotel-stars">
          <span v-for="star in hotelData.stars" :key="star">★</span>
        </div>

        <div class="hotel-actions">
          <button class="action-btn" @click="$emit('share')">
            <svg
              width="16"
              height="16"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            >
              <circle cx="18" cy="5" r="3"></circle>
              <circle cx="6" cy="12" r="3"></circle>
              <circle cx="18" cy="19" r="3"></circle>
              <line x1="8.59" y1="13.51" x2="15.42" y2="17.49"></line>
              <line x1="15.41" y1="6.51" x2="8.59" y2="10.49"></line>
            </svg>
            Share
          </button>

          <!-- Favorite Button - Heart Icon & Notification from HotelCard.vue -->
          <button
            class="action-btn favorite-action-btn"
            @click="toggleFavorite"
          >
            <svg
              width="16"
              height="16"
              viewBox="0 0 24 24"
              fill="none"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            >
              <path
                v-if="!isFavorite"
                d="M20.84 4.61a5.5 5.5 0 0 0-7.78 0L12 5.67l-1.06-1.06a5.5 5.5 0 0 0-7.78 7.78l1.06 1.06L12 21.23l7.78-7.78 1.06-1.06a5.5 5.5 0 0 0 0-7.78z"
                stroke="currentColor"
              />
              <path
                v-else
                d="M20.84 4.61a5.5 5.5 0 0 0-7.78 0L12 5.67l-1.06-1.06a5.5 5.5 0 0 0-7.78 7.78l1.06 1.06L12 21.23l7.78-7.78 1.06-1.06a5.5 5.5 0 0 0 0-7.78z"
                fill="#dc3545"
                stroke="#dc3545"
              />
            </svg>
            Save
          </button>
        </div>
      </div>

      <h1 class="hotel-name">{{ hotelData.name || "Hotel Name" }}</h1>

      <div v-if="hotelData.location" class="hotel-location">
        <svg
          width="16"
          height="16"
          viewBox="0 0 24 24"
          fill="none"
          class="location-icon"
        >
          <path
            d="M21 10c0 7-9 13-9 13s-9-6-9-13a9 9 0 0 1 18 0z"
            stroke="currentColor"
            stroke-width="2"
          />
          <circle
            cx="12"
            cy="10"
            r="3"
            stroke="currentColor"
            stroke-width="2"
          />
        </svg>
        {{ hotelData.location }}
      </div>
    </div>

    <!-- Full Gallery Modal - Matched to provided image and HotelCard.vue -->
    <div
      v-if="showGalleryModal"
      class="modal-overlay"
      @click.self="closeGalleryModal"
    >
      <div class="gallery-modal-content-revamped">
        <!-- Close Button -->
        <button class="close-modal-btn-revamped" @click="closeGalleryModal">
          <svg
            width="24"
            height="24"
            viewBox="0 0 24 24"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              d="M18 6L6 18"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
            <path
              d="M6 6L18 18"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
        </button>
        <img
          :src="galleryImages[activeGalleryIndex]"
          :alt="`${hotelData.name} gallery image ${activeGalleryIndex + 1}`"
          class="modal-image"
        />
        <!-- Navigation for Modal -->
        <div class="gallery-modal-nav-revamped">
          <button
            @click="previousGalleryImage"
            :disabled="galleryImages.length <= 1"
          >
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none">
              <path
                d="M15 18L9 12L15 6"
                stroke="white"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
              />
            </svg>
          </button>
          <span>{{ activeGalleryIndex + 1 }} / {{ galleryImages.length }}</span>
          <button
            @click="nextGalleryImage"
            :disabled="galleryImages.length <= 1"
          >
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none">
              <path
                d="M9 18L15 12L9 6"
                stroke="white"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
              />
            </svg>
          </button>
        </div>
      </div>
    </div>

    <!-- Notification Container - From HotelCard.vue -->
    <transition name="fade">
      <div v-if="showNotification" class="notification">
        {{ notificationMessage }}
      </div>
    </transition>
  </div>
</template>

<script>
export default {
  name: "HotelDetailHeader",
  props: {
    hotelData: {
      type: Object,
      required: true,
      default: () => ({
        name: "Jumeirah Beach Hotel",
        location: "Jumeirah Beach, Dubai, United Arab Emirates",
        stars: 5, // Example: numeric stars for v-for "★"
        rating: 4.5,
        reviewCount: "5,124",
        mainImage:
          "https://images.unsplash.com/photo-1566073771259-6a8506099945?w=800&h=600&fit=crop",
        gallery: [
          "https://images.unsplash.com/photo-1566073771259-6a8506099945?w=1200&h=800&fit=crop", // Main image
          "https://images.unsplash.com/photo-1520250497591-112f2f40a3f4?w=800&h=600&fit=crop", // Side thumbnail 1
          "https://images.unsplash.com/photo-1571896349842-33c89424de2d?w=800&h=600&fit=crop", // Side thumbnail 2
          "https://images.unsplash.com/photo-1582719508461-905c673771fd?w=800&h=600&fit=crop", // Additional gallery image 1
          "https://images.unsplash.com/photo-1584132967334-10e028bd69f7?w=800&h=600&fit=crop", // Additional gallery image 2
          "https://images.unsplash.com/photo-1571896349842-33c89424de2d?w=800&h=600&fit=crop", // Additional gallery image 3
        ],
        description:
          "Distinctive wave-shaped beachfront resort offering uninterrupted views of the Arabian Gulf and world-class facilities.",
        amenities: [
          "Private Beach",
          "Wild Wadi Waterpark Access",
          "Family Entertainment",
        ],
      }),
    },
    initialFavorite: {
      type: Boolean,
      default: false,
    },
  },
  emits: [
    "toggle-favorite",
    "show-hotel-details",
    "show-map",
    "show-alternative-flights",
    "share", // Added for the share button
  ],
  data() {
    return {
      isFavorite: this.initialFavorite,
      currentMainImageIndex: 0,
      showGalleryModal: false,
      activeGalleryIndex: 0,
      showNotification: false, // For notification system
      notificationMessage: "", // For notification message
      thumbnailStartIndex: 0, // Starting index for visible thumbnails
    };
  },
  computed: {
    galleryImages() {
      // Ensure gallery array exists and is populated
      if (
        !this.hotelData ||
        !Array.isArray(this.hotelData.gallery) ||
        this.hotelData.gallery.length === 0
      ) {
        return this.hotelData && this.hotelData.mainImage
          ? [this.hotelData.mainImage]
          : [];
      }
      return this.hotelData.gallery;
    },
    hasMultipleImages() {
      return this.galleryImages.length > 1;
    },
    totalImages() {
      return this.galleryImages.length;
    },
    currentMainImage() {
      if (this.galleryImages.length === 0) {
        return this.hotelData?.mainImage || "";
      }
      return (
        this.galleryImages[this.currentMainImageIndex] ||
        this.hotelData?.mainImage ||
        ""
      );
    },
    // Get thumbnails excluding the main image (first image)
    thumbnailImages() {
      return this.galleryImages.slice(1);
    },
    // Get fixed 2 thumbnails (always show first 2 thumbnails after main image)
    fixedThumbnails() {
      return this.thumbnailImages.slice(0, 2);
    },
  },
  watch: {
    // Remove thumbnail scroll watching since we're using fixed thumbnails
  },
  methods: {
    toggleFavorite() {
      this.isFavorite = !this.isFavorite;
      this.$emit("toggle-favorite", {
        hotel: this.hotelData,
        isFavorite: this.isFavorite,
      });
      if (this.isFavorite) {
        this.showNotificationMessage(
          "Item has been added to your favorites list."
        );
      } else {
        this.showNotificationMessage("Item removed from favorites.");
      }
    },

    // Notification system from HotelCard.vue
    showNotificationMessage(message) {
      this.notificationMessage = message;
      this.showNotification = true;
      setTimeout(() => {
        this.showNotification = false;
        this.notificationMessage = "";
      }, 3000); // Notification visible for 3 seconds
    },

    // Main image navigation
    nextMainImage() {
      if (this.hasMultipleImages) {
        this.currentMainImageIndex =
          (this.currentMainImageIndex + 1) % this.totalImages;
      }
    },

    previousMainImage() {
      if (this.hasMultipleImages) {
        this.currentMainImageIndex =
          this.currentMainImageIndex === 0
            ? this.totalImages - 1
            : this.currentMainImageIndex - 1;
      }
    },

    setMainImage(index) {
      this.currentMainImageIndex = index;
    },

    // Gallery modal methods (Updated to match provided image and HotelCard.vue)
    openGalleryModal(startIndex = 0) {
      this.activeGalleryIndex = startIndex;
      this.showGalleryModal = true;
      document.body.style.overflow = "hidden"; // Disable scroll on body
    },

    closeGalleryModal() {
      this.showGalleryModal = false;
      document.body.style.overflow = ""; // Re-enable scroll on body
    },

    nextGalleryImage() {
      if (this.galleryImages.length > 1) {
        this.activeGalleryIndex =
          (this.activeGalleryIndex + 1) % this.galleryImages.length;
      }
    },

    previousGalleryImage() {
      if (this.galleryImages.length > 1) {
        this.activeGalleryIndex =
          this.activeGalleryIndex === 0
            ? this.galleryImages.length - 1
            : this.activeGalleryIndex - 1;
      }
    },

    setActiveGalleryImage(index) {
      this.activeGalleryIndex = index;
    },
  },
};
</script>

<style scoped>
/* Base styles and fonts (assumed from previous context or global styles) */
body {
  font-family: "Inter", sans-serif;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.hotel-detail-header {
  margin-bottom: 30px;
}

/* Gallery Container - Main layout for the image section */
.gallery-container {
  display: grid;
  grid-template-columns: 3fr 1fr; /* Maksimalkan area main image */
  gap: 12px;
  margin-bottom: 25px;
  border-radius: 12px;
  overflow: hidden;
  height: 375px; /* Tinggi dikurangi 25% */
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.05);
  background: #fff;
}

.main-image-container {
  position: relative;
  overflow: hidden;
  background: #f5f5f5;
  border-radius: 12px;
  width: 100%;
  height: 375px; /* EXPLICITLY SET HEIGHT TO MATCH CONTAINER */
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.image-wrapper {
  width: 100%;
  height: 375px; /* EXPLICITLY SET HEIGHT TO MATCH CONTAINER */
  position: relative;
  display: flex;
}

.main-image {
  width: 100%;
  height: 100%;
  object-fit: cover; /* Menggunakan cover untuk memaksimalkan area */
  object-position: center center;
  cursor: pointer;
  transition: transform 0.3s ease;
}

.main-image-container:hover .gallery-nav-overlay {
  opacity: 1;
  pointer-events: all;
}

/* Gallery Navigation Overlay - FIXED HEIGHT INHERITANCE */
.gallery-nav-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  width: 100%;
  height: 375px; /* EXPLICITLY SET HEIGHT TO MATCH CONTAINER */
  opacity: 0;
  transition: opacity 0.3s ease;
  pointer-events: none;
}

.nav-arrow {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  background: rgba(0, 0, 0, 0.7);
  border: none;
  border-radius: 50%;
  width: 50px;
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
  z-index: 10;
}

.nav-arrow-left {
  left: 20px;
}

.nav-arrow-right {
  right: 20px;
}

.nav-arrow:hover {
  background: rgba(0, 0, 0, 0.9);
  transform: translateY(-50%) scale(1.1);
}

.main-image:hover {
  transform: scale(1.02); /* Sedikit zoom effect saat hover */
}
.image-counter {
  position: absolute;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  background: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 8px 16px;
  border-radius: 20px;
  font-size: 14px;
  font-weight: 500;
  z-index: 10;
  max-height: fit-content; /* Ensure it doesn't overflow */
}

/* View All Photos Button - RESPONSIVE POSITIONING */
.view-all-photos-btn {
  position: absolute;
  bottom: 20px;
  right: 20px;
  background: rgba(0, 0, 0, 0.7);
  color: white;
  border: none;
  padding: 10px 16px;
  border-radius: 8px;
  font-size: 14px;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 8px;
  transition: all 0.3s ease;
  z-index: 10;
  max-height: fit-content; /* Ensure it doesn't overflow */
}

.view-all-photos-btn:hover {
  background: rgba(0, 0, 0, 0.9);
}

/* Right Side Thumbnails Wrapper - HAPUS PADDING */
.side-thumbnails-wrapper {
  display: flex;
  flex-direction: column;
  height: 100%;
  gap: 8px;
  position: relative;
  box-sizing: border-box;
}

/* Visible Thumbnails Container - FIXED HEIGHT CALCULATION */
.visible-thumbnails-container {
  display: flex;
  flex-direction: column;
  gap: 8px;
  height: 100%;
  flex: 1;
}

/* View All Photos Overlay Button - Positioned at bottom */
.view-all-photos-overlay-btn {
  background: rgba(0, 0, 0, 0.8);
  color: white;
  border: none;
  padding: 12px 16px;
  border-radius: 8px;
  font-size: 13px;
  font-weight: 500;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  transition: all 0.3s ease;
  flex-shrink: 0;
  height: 48px;
}

.view-all-photos-overlay-btn:hover {
  background: rgba(0, 0, 0, 0.9);
  transform: translateY(-1px);
}

.thumbnail-item {
  width: 100%;
  position: relative;
  border-radius: 8px;
  overflow: hidden;
  cursor: pointer;
  transition: all 0.3s ease;
  border: 2px solid transparent;
  flex: 1;
  height: calc(
    50% - 4px
  ); /* Explicit height calculation: 50% minus half of gap */
}

.thumbnail-item:first-child {
  /* No special styling needed */
}

.thumbnail-item:last-child {
  /* No special styling needed */
}

.thumbnail-item img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: center center;
  display: block;
}

.thumbnail-item:hover {
  transform: scale(1.02);
  border-color: #ac7872;
}

.thumbnail-item.active {
  border-color: #ac7872;
  box-shadow: 0 0 0 1px #ac7872;
}

/* Hotel Information Section */
.hotel-info-section {
  padding: 0;
  margin-bottom: 30px;
}

.hotel-header-meta {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
}

/* Star Rating - Styles from HotelCard.vue */
.hotel-stars {
  color: #ffa500;
  font-size: 18px;
  letter-spacing: 2px;
  margin-bottom: 0;
}

.hotel-name {
  font-size: 32px;
  font-weight: 700;
  margin-bottom: 8px;
  color: #333;
  line-height: 1.2;
}

.hotel-location {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #666;
  margin-bottom: 20px;
  font-size: 15px;
}

.location-icon {
  color: #ac7872;
}

.hotel-actions {
  display: flex;
  gap: 15px;
  flex-wrap: wrap;
}

.action-btn {
  display: flex;
  align-items: center;
  gap: 6px;
  color: #555;
  text-decoration: none;
  font-size: 14px;
  font-weight: 500;
  background: #f0f0f0;
  border: 1px solid #ddd;
  padding: 8px 15px;
  border-radius: 25px;
  cursor: pointer;
  transition: all 0.2s;
}

.action-btn:hover {
  background: #e0e0e0;
  color: #333;
}

.action-btn svg {
  stroke: currentColor;
}

.favorite-action-btn path[fill="#dc3545"] {
  fill: #dc3545;
  stroke: #dc3545;
}

/* Full Gallery Modal - Matched to provided image and HotelCard.vue */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.95);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 9999;
  padding: 20px;
}

.gallery-modal-content-revamped {
  position: relative;
  background: #1a1a1a;
  border-radius: 8px;
  overflow: hidden;
  max-width: 95vw;
  max-height: 95vh;
  width: auto;
  height: auto;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
}

.close-modal-btn-revamped {
  position: absolute;
  top: 15px;
  right: 15px;
  background: rgba(255, 255, 255, 0.1);
  border: none;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  z-index: 10000;
  color: white;
  transition: background-color 0.2s ease;
}

.close-modal-btn-revamped:hover {
  background: rgba(255, 255, 255, 0.2);
}

.close-modal-btn-revamped svg {
  stroke: white;
}

.modal-image {
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
  display: block;
  margin: auto;
  min-height: 300px;
  width: auto;
  height: auto;
}

.gallery-modal-nav-revamped {
  position: absolute;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  align-items: center;
  gap: 15px;
  background: rgba(0, 0, 0, 0.7);
  padding: 10px 20px;
  border-radius: 25px;
  color: white;
  font-size: 14px;
  z-index: 10000;
}

.gallery-modal-nav-revamped button {
  background: none;
  border: none;
  padding: 0;
  cursor: pointer;
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 30px;
  height: 30px;
  border-radius: 50%;
  transition: background-color 0.2s ease;
}

.gallery-modal-nav-revamped button:hover:not(:disabled) {
  background: rgba(255, 255, 255, 0.1);
}

.gallery-modal-nav-revamped button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* Notification Styles - From HotelCard.vue */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.notification {
  position: fixed;
  bottom: 30px;
  left: 50%;
  transform: translateX(-50%);
  background-color: #333;
  color: white;
  padding: 12px 20px;
  border-radius: 8px;
  font-size: 15px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  z-index: 10001;
  white-space: nowrap;
}

/* Responsive Design */
/* Responsive adjustments untuk overlay elements */
@media (max-width: 1200px) {
  .gallery-container {
    height: 337px; /* 25% reduction dari 450px */
  }

  .main-image-container {
    height: 337px; /* MATCH CONTAINER HEIGHT */
  }

  .image-wrapper {
    height: 337px; /* MATCH CONTAINER HEIGHT */
  }

  .gallery-nav-overlay {
    height: 337px; /* MATCH CONTAINER HEIGHT */
  }

  .thumbnail-item {
    height: calc(50% - 4px); /* Keep same calculation untuk responsive */
  }

  .image-counter,
  .view-all-photos-btn {
    bottom: 15px; /* Adjust positioning untuk container yang lebih kecil */
    padding: 6px 12px;
    font-size: 13px;
  }
}

@media (max-width: 1024px) {
  .gallery-container {
    height: 300px; /* 25% reduction dari 400px */
    grid-template-columns: 2.5fr 1fr;
  }

  .main-image-container {
    height: 300px; /* MATCH CONTAINER HEIGHT */
  }

  .image-wrapper {
    height: 300px; /* MATCH CONTAINER HEIGHT */
  }

  .gallery-nav-overlay {
    height: 300px; /* MATCH CONTAINER HEIGHT */
  }

  .thumbnail-item {
    height: calc(50% - 4px); /* Keep same calculation untuk responsive */
  }

  .hotel-name {
    font-size: 28px;
  }

  .image-counter,
  .view-all-photos-btn {
    bottom: 12px; /* Adjust untuk container yang lebih kecil */
    padding: 6px 10px;
    font-size: 12px;
  }
}

@media (max-width: 768px) {
  .gallery-container {
    grid-template-columns: 1fr;
    grid-template-rows: 300px;
    height: auto;
    gap: 15px;
  }

  .main-image-container {
    height: 300px; /* EXPLICIT HEIGHT FOR MOBILE */
  }

  .image-wrapper {
    height: 300px; /* EXPLICIT HEIGHT FOR MOBILE */
  }

  .gallery-nav-overlay {
    height: 300px; /* EXPLICIT HEIGHT FOR MOBILE */
  }

  /* Hide thumbnails on mobile */
  .side-thumbnails-wrapper {
    display: none;
  }

  .image-counter,
  .view-all-photos-btn {
    bottom: 10px; /* Adjust untuk mobile */
    padding: 4px 8px;
    font-size: 11px;
  }

  .view-all-photos-btn {
    right: 10px;
  }

  .gallery-nav-overlay {
    display: none;
  }

  .hotel-header-meta {
    flex-direction: column-reverse;
    align-items: flex-start;
    margin-bottom: 10px;
  }

  .hotel-actions {
    width: 100%;
    justify-content: flex-end;
    margin-bottom: 10px;
  }

  .hotel-name {
    font-size: 24px;
  }

  .hotel-location {
    font-size: 14px;
    margin-bottom: 15px;
  }

  .modal-overlay {
    padding: 0;
  }

  .gallery-modal-content-revamped {
    border-radius: 0;
    max-width: 100vw;
    max-height: 100vh;
    width: 100%;
    height: 100%;
  }

  .close-modal-btn-revamped {
    top: 10px;
    right: 10px;
    width: 36px;
    height: 36px;
  }

  .gallery-modal-nav-revamped {
    bottom: 10px;
    padding: 8px 15px;
    font-size: 13px;
  }
}

@media (max-width: 480px) {
  .gallery-container {
    grid-template-rows: 250px;
    gap: 12px;
  }

  .main-image-container {
    height: 250px; /* EXPLICIT HEIGHT FOR SMALL MOBILE */
  }

  .image-wrapper {
    height: 250px; /* EXPLICIT HEIGHT FOR SMALL MOBILE */
  }

  .gallery-nav-overlay {
    height: 250px; /* EXPLICIT HEIGHT FOR SMALL MOBILE */
  }

  .hotel-name {
    font-size: 20px;
  }

  .hotel-location {
    font-size: 13px;
  }

  .action-btn {
    padding: 6px 10px;
    font-size: 12px;
  }

  .hotel-actions {
    gap: 8px;
  }

  .image-counter,
  .view-all-photos-btn {
    bottom: 10px;
    padding: 4px 8px;
    font-size: 11px;
  }

  .view-all-photos-btn {
    right: 10px;
  }
}

/* Additional Enhancement - Smooth animations */
.thumbnail-item {
  position: relative;
}

.thumbnail-item::after {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(172, 120, 114, 0.1);
  opacity: 0;
  transition: opacity 0.3s ease;
}

.thumbnail-item.active::after,
.thumbnail-item:hover::after {
  opacity: 1;
}

/* Loading state for images */
.main-image,
.thumbnail-item img {
  background: #f8f9fa;
}

/* Focus states for accessibility */
.nav-arrow:focus,
.thumbnail-item:focus,
.view-all-photos-btn:focus,
.view-all-photos-overlay-btn:focus,
.action-btn:focus {
  outline: 2px solid #ac7872;
  outline-offset: 2px;
}

/* Improve visual hierarchy */
.gallery-container {
  background: #fff;
}

.main-image-container {
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.side-thumbnails-wrapper {
  background: rgba(248, 249, 250, 0.5);
  border-radius: 8px;
}
</style>
