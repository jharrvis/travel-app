<template>
  <div class="hotel-card" @click="$emit('hotel-click', hotel)">
    <!-- BARIS PERTAMA: DETAIL HOTEL - 2 KOLOM -->
    <div class="hotel-row">
      <!-- KOLOM 1: Gallery Gambar -->
      <div class="hotel-image-container">
        <!-- Image Gallery -->
        <div class="image-gallery">
          <img :src="getCurrentImage()" :alt="hotel.name" class="hotel-image" />

          <!-- Gallery Navigation Arrows -->
          <button
            class="gallery-arrow gallery-arrow-left"
            @click.stop="previousImage"
            v-if="hasMultipleImages"
          >
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
              <path
                d="M15 18L9 12L15 6"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
              />
            </svg>
          </button>

          <button
            class="gallery-arrow gallery-arrow-right"
            @click.stop="nextImage"
            v-if="hasMultipleImages"
          >
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
              <path
                d="M9 18L15 12L9 6"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
              />
            </svg>
          </button>

          <!-- Top Pick Badge -->
          <div class="top-pick-badge" v-if="hotel.top_pick">Top pick</div>

          <!-- Images Button -->
          <div class="images-btn" @click.stop="openGallery">
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
              <rect
                x="3"
                y="3"
                width="18"
                height="18"
                rx="2"
                ry="2"
                stroke="currentColor"
                stroke-width="2"
              />
              <circle cx="8.5" cy="8.5" r="1.5" fill="currentColor" />
              <polyline
                points="21,15 16,10 5,21"
                stroke="currentColor"
                stroke-width="2"
              />
            </svg>
            Images
          </div>
        </div>
      </div>

      <!-- KOLOM 2: Hotel Content - Flex Column Layout -->
      <div class="hotel-content">
        <!-- Hotel Name dengan Info Icon -->
        <div class="hotel-title-row">
          <h3 class="hotel-name">{{ hotel.name }}</h3>
          <div
            class="info-icon"
            v-if="hotel.description"
            @click.stop="showInfo"
          >
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
              <circle
                cx="12"
                cy="12"
                r="10"
                stroke="currentColor"
                stroke-width="2"
              />
              <path d="M12,8 L12,16" stroke="currentColor" stroke-width="2" />
              <circle cx="12" cy="6" r="1" fill="currentColor" />
            </svg>
          </div>
        </div>

        <!-- Star Rating -->
        <div class="hotel-stars">
          <span v-for="star in hotel.stars" :key="star">★</span>
        </div>

        <!-- Description -->
        <div class="hotel-description" v-if="hotel.description">
          {{ hotel.description }}
        </div>

        <!-- Amenities dengan checkmarks -->
        <div class="hotel-amenities" v-if="hotel.amenities">
          <div
            v-for="amenity in hotel.amenities.slice(0, 3)"
            :key="amenity"
            class="amenity-item"
          >
            <svg
              width="16"
              height="16"
              viewBox="0 0 24 24"
              fill="none"
              class="check-icon"
            >
              <polyline
                points="20,6 9,17 4,12"
                stroke="#28a745"
                stroke-width="2"
              />
            </svg>
            {{ amenity }}
            <div class="info-tooltip" v-if="amenity.includes('Waterpark')">
              <svg width="14" height="14" viewBox="0 0 24 24" fill="none">
                <circle
                  cx="12"
                  cy="12"
                  r="10"
                  stroke="currentColor"
                  stroke-width="2"
                />
                <path d="M12,8 L12,16" stroke="currentColor" stroke-width="2" />
                <circle cx="12" cy="6" r="1" fill="currentColor" />
              </svg>
            </div>
          </div>
        </div>

        <!-- Date, Nights, and Pricing - Moved to bottom of this column -->
        <div class="pricing-info">
          <!-- Date and Nights Info -->
          <div class="date-nights-grid">
            <div class="info-block">
              <div class="info-label">DATE</div>
              <div class="info-value">
                {{ formatDate(searchData.startDate) }}
              </div>
            </div>
            <div class="info-block">
              <div class="info-label">NIGHTS IN RESORT</div>
              <div class="info-value">
                {{ calculateNights(searchData.startDate, searchData.endDate) }}
              </div>
            </div>
          </div>

          <!-- Pricing -->
          <div class="price-breakdown">
            <div class="adult-price">
              <span class="adult-label">Adult:</span>
              <span class="adult-amount">{{
                formatPrice(
                  hotel.pricing?.adult_price || extractPrice(hotel.currentPrice)
                )
              }}</span>
            </div>
            <div class="total-price">
              <span class="total-label">Total</span>
              <span class="total-amount">{{
                formatPrice(
                  hotel.pricing?.total_price ||
                    extractPrice(hotel.currentPrice) * 2
                )
              }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- BARIS KEDUA: FLIGHT DETAILS -->
    <div class="flight-row">
      <!-- Flight Details -->
      <div class="flight-detail-section">
        <div class="detail-header">Flight details</div>
        <div class="detail-content">
          <div class="detail-row">
            <span class="airline-name">{{
              hotel.flight_details?.airline || "Emirates"
            }}</span>
            <div class="baggage-info">
              <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
                <rect
                  x="3"
                  y="4"
                  width="18"
                  height="16"
                  rx="2"
                  stroke="currentColor"
                  stroke-width="2"
                />
                <line
                  x1="9"
                  y1="9"
                  x2="15"
                  y2="9"
                  stroke="currentColor"
                  stroke-width="2"
                />
              </svg>
              <span>{{
                hotel.flight_details?.baggage || "30kgs included"
              }}</span>
            </div>
          </div>
          <div class="detail-row">
            <span class="flight-class">{{
              hotel.flight_details?.class || "Economy"
            }}</span>
            <a href="#" class="flight-info-link" @click.stop="showFlightInfo"
              >Flight information</a
            >
          </div>
        </div>
      </div>

      <!-- Room Details -->
      <div class="room-detail-section">
        <div class="detail-header">Room details</div>
        <div class="detail-content">
          <div class="detail-row">
            <span class="room-type">{{
              hotel.room_type || "Palace Superior Room"
            }}</span>
            <a href="#" class="change-link" @click.stop="changeRoom"
              >Change room & meal plan</a
            >
          </div>
          <div class="room-board">
            <span>{{ hotel.board_basis || "Half Board" }}</span>
          </div>
        </div>
      </div>

      <!-- Deposit and Continue Section -->
      <div class="deposit-continue-section">
        <div class="deposit-info">
          <span>Confirm this holiday for </span>
          <strong
            >{{
              formatPrice(
                hotel.pricing?.deposit ||
                  Math.round(extractPrice(hotel.currentPrice) * 0.2)
              )
            }}
            deposit.</strong
          >
          <svg
            width="16"
            height="16"
            viewBox="0 0 24 24"
            fill="none"
            class="deposit-icon"
          >
            <rect
              x="3"
              y="4"
              width="18"
              height="16"
              rx="2"
              stroke="currentColor"
              stroke-width="2"
            />
            <line
              x1="9"
              y1="9"
              x2="15"
              y2="9"
              stroke="currentColor"
              stroke-width="2"
            />
          </svg>
          <a href="#" class="deposit-link" @click.stop="showDepositBreakdown"
            >See deposit breakdown</a
          >
        </div>

        <div class="continue-section">
          <div class="warning-icon" v-if="showWarning">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
              <circle
                cx="12"
                cy="12"
                r="10"
                stroke="#dc3545"
                stroke-width="2"
              />
              <line
                x1="12"
                y1="8"
                x2="12"
                y2="12"
                stroke="#dc3545"
                stroke-width="2"
              />
              <line
                x1="12"
                y1="16"
                x2="12.01"
                y2="16"
                stroke="#dc3545"
                stroke-width="2"
              />
            </svg>
          </div>
          <button
            class="continue-btn"
            @click.stop="$emit('hotel-click', hotel)"
          >
            Continue
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "HotelCard",
  props: {
    hotel: {
      type: Object,
      required: true,
      validator(hotel) {
        return (
          hotel &&
          typeof hotel.id !== "undefined" &&
          typeof hotel.name === "string"
        );
      },
    },
    searchData: {
      type: Object,
      default: () => ({
        startDate: "",
        endDate: "",
      }),
    },
  },
  emits: ["hotel-click"],
  data() {
    return {
      showWarning: false,
      currentImageIndex: 0,
    };
  },
  computed: {
    hasMultipleImages() {
      return this.hotel.gallery && this.hotel.gallery.length > 1;
    },
  },
  methods: {
    getCurrentImage() {
      if (this.hotel.gallery && this.hotel.gallery.length > 0) {
        return this.hotel.gallery[this.currentImageIndex];
      }
      return this.hotel.main_image || this.hotel.image || "";
    },

    nextImage() {
      if (this.hasMultipleImages) {
        this.currentImageIndex =
          (this.currentImageIndex + 1) % this.hotel.gallery.length;
      }
    },

    previousImage() {
      if (this.hasMultipleImages) {
        this.currentImageIndex =
          this.currentImageIndex === 0
            ? this.hotel.gallery.length - 1
            : this.currentImageIndex - 1;
      }
    },

    formatDate(dateString) {
      if (!dateString) return "1 Oct 2025";

      const date = new Date(dateString);
      return date.toLocaleDateString("en-GB", {
        day: "numeric",
        month: "short",
        year: "numeric",
      });
    },

    calculateNights(startDate, endDate) {
      if (!startDate || !endDate) return 7;

      const start = new Date(startDate);
      const end = new Date(endDate);
      const diffTime = Math.abs(end - start);
      const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));

      return diffDays;
    },

    formatPrice(amount) {
      if (typeof amount === "string") {
        return amount;
      }
      return `£${amount?.toLocaleString() || "0"}`;
    },

    extractPrice(priceString) {
      if (typeof priceString === "number") return priceString;
      if (typeof priceString === "string") {
        const match = priceString.match(/[\d,]+/);
        return match ? parseInt(match[0].replace(/,/g, "")) : 0;
      }
      return 0;
    },

    openGallery() {
      console.log("Open gallery for hotel:", this.hotel.name);
    },

    showInfo() {
      console.log("Show info for hotel:", this.hotel.name);
    },

    showFlightInfo() {
      console.log("Show flight information");
    },

    changeRoom() {
      console.log("Change room & meal plan");
    },

    showDepositBreakdown() {
      console.log("Show deposit breakdown");
    },
  },
};
</script>

<style scoped>
/* Base Hotel Card Styles - 2 Rows Layout */
.hotel-card {
  background: white;
  border: 1px solid #ddd;
  overflow: hidden;
  transition: all 0.3s ease;
  cursor: pointer;
  display: flex;
  flex-direction: column;
  width: 100%;
  margin-bottom: 1px;
}

.hotel-card:hover {
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
}

/* BARIS PERTAMA: DETAIL HOTEL - 2 KOLOM */
.hotel-row {
  display: flex;
  min-height: 280px;
}

/* KOLOM 1: Image Gallery Section */
.hotel-image-container {
  position: relative;
  width: 320px;
  flex-shrink: 0;
  overflow: hidden;
}

.image-gallery {
  position: relative;
  width: 100%;
  height: 100%;
}

.hotel-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}

.gallery-arrow {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  background: rgba(255, 255, 255, 0.9);
  border: none;
  width: 32px;
  height: 32px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.2s;
  z-index: 3;
  color: #333;
}

.gallery-arrow:hover {
  background: white;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
}

.gallery-arrow-left {
  left: 10px;
}

.gallery-arrow-right {
  right: 10px;
}

.top-pick-badge {
  position: absolute;
  top: 12px;
  left: 12px;
  background: #dc3545;
  color: white;
  padding: 6px 12px;
  border-radius: 4px;
  font-size: 12px;
  font-weight: 600;
  z-index: 2;
}

.images-btn {
  position: absolute;
  bottom: 12px;
  left: 12px;
  background: rgba(255, 255, 255, 0.9);
  border: 1px solid #ddd;
  padding: 6px 10px;
  border-radius: 4px;
  font-size: 12px;
  display: flex;
  align-items: center;
  gap: 6px;
  cursor: pointer;
  transition: background 0.2s;
  z-index: 2;
}

.images-btn:hover {
  background: white;
}

/* KOLOM 2: Hotel Content Section - Flex Column Layout */
.hotel-content {
  flex: 1;
  padding: 20px;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.hotel-title-row {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
}

.hotel-name {
  font-size: 20px;
  font-weight: 600;
  color: #333;
  margin: 0;
  flex: 1;
  line-height: 1.3;
}

.info-icon {
  background: #007bff;
  color: white;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  flex-shrink: 0;
  margin-left: 10px;
}

.hotel-stars {
  color: #ffa500;
  font-size: 16px;
}

.hotel-description {
  color: #333;
  font-size: 14px;
  line-height: 1.5;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.hotel-amenities {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.amenity-item {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
  color: #333;
}

.check-icon {
  flex-shrink: 0;
}

.info-tooltip {
  margin-left: 4px;
  color: #666;
  cursor: pointer;
}

/* Pricing Info - Moved to bottom of column 2 */
.pricing-info {
  margin-top: auto;
  padding-top: 16px;
  border-top: 1px solid #eee;
}

.date-nights-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 16px;
  margin-bottom: 16px;
  text-align: center;
}

.info-block {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.info-label {
  font-size: 10px;
  font-weight: 600;
  color: #666;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 4px;
}

.info-value {
  font-size: 14px;
  font-weight: 600;
  color: #333;
}

.price-breakdown {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.adult-price {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.adult-label {
  font-size: 14px;
  color: #666;
}

.adult-amount {
  font-size: 18px;
  font-weight: 600;
  color: #333;
}

.total-price {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  gap: 2px;
}

.total-label {
  font-size: 14px;
  color: #666;
}

.total-amount {
  font-size: 20px;
  font-weight: 700;
  color: #333;
}

/* BARIS KEDUA: FLIGHT DETAILS */
.flight-row {
  display: flex;
  padding: 16px 20px;
  background: #f8f9fa;
  border-top: 1px solid #eee;
  align-items: center;
  gap: 20px;
}

.flight-detail-section,
.room-detail-section {
  flex: 1;
}

.detail-header {
  font-size: 14px;
  font-weight: 600;
  color: #333;
  margin-bottom: 6px;
}

.detail-content {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.detail-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 14px;
  color: #333;
}

.baggage-info {
  display: flex;
  align-items: center;
  gap: 4px;
  font-size: 12px;
  color: #666;
}

.flight-info-link,
.change-link,
.deposit-link {
  color: #007bff;
  text-decoration: none;
  font-size: 12px;
}

.flight-info-link:hover,
.change-link:hover,
.deposit-link:hover {
  text-decoration: underline;
}

.room-board {
  color: #666;
  font-size: 14px;
  margin-top: 4px;
}

/* Deposit and Continue Section */
.deposit-continue-section {
  display: flex;
  align-items: center;
  gap: 20px;
  flex: 1;
  justify-content: space-between;
}

.deposit-info {
  font-size: 13px;
  color: #333;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 4px;
  flex: 1;
}

.deposit-icon {
  margin: 0 4px;
}

.continue-section {
  display: flex;
  align-items: center;
  gap: 12px;
}

.warning-icon {
  color: #dc3545;
}

.continue-btn {
  background: #17a2b8;
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 6px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.2s;
  white-space: nowrap;
}

.continue-btn:hover {
  background: #138496;
}

/* Responsive Design */
@media (max-width: 1024px) {
  .hotel-row {
    flex-direction: column;
    min-height: auto;
  }

  .hotel-image-container {
    width: 100%;
    height: 200px;
  }

  .pricing-section {
    width: 100%;
    border-left: none;
    border-top: 1px solid #eee;
  }

  .flight-row {
    flex-direction: column;
    align-items: stretch;
    gap: 16px;
  }

  .deposit-continue-section {
    flex-direction: column;
    align-items: stretch;
    gap: 12px;
  }

  .continue-section {
    justify-content: flex-end;
  }
}

@media (max-width: 768px) {
  .hotel-content {
    padding: 16px;
  }

  .pricing-section {
    padding: 16px;
  }

  .flight-row {
    padding: 12px 16px;
  }

  .hotel-name {
    font-size: 16px;
  }

  .hotel-description {
    -webkit-line-clamp: 1;
  }

  .amenity-item {
    font-size: 13px;
  }

  .date-nights-grid {
    grid-template-columns: 1fr 1fr;
    gap: 12px;
  }

  .price-breakdown {
    flex-direction: column;
    align-items: stretch;
    gap: 8px;
  }

  .total-price {
    align-items: flex-start;
  }
}
</style>
