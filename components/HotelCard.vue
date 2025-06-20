<template>
  <div class="hotel-card" @click="$emit('hotel-click', hotel)">
    <div class="hotel-image-container">
      <img :src="hotel.image" :alt="hotel.name" class="hotel-image" />

      <!-- Free child stay badge (top left) -->
      <div class="free-child-badge" v-if="hotel.freeChildStay">
        Free child stay
      </div>

      <!-- Rating circles (top right) -->
      <div class="rating-circles-overlay">
        <div
          v-for="i in 5"
          :key="i"
          class="rating-circle"
          :class="
            i <= Math.floor(hotel.rating) ? 'circle-filled' : 'circle-empty'
          "
        ></div>
      </div>

      <!-- Save amount (bottom left) -->
      <div class="save-amount" v-if="hotel.savings">
        {{ hotel.savings }}
      </div>
    </div>

    <div class="hotel-content">
      <!-- Hotel name -->
      <h3 class="hotel-name">{{ hotel.name }}</h3>

      <!-- Hotel location -->
      <div class="hotel-location">{{ hotel.location }}</div>

      <!-- Price section -->
      <div class="hotel-pricing-section">
        <div class="price-container">
          <div class="price-label">From</div>
          <div class="price-row">
            <span class="price-amount">{{ hotel.currentPrice }}</span>
            <span class="price-note">{{ hotel.priceNote }}</span>
          </div>
        </div>

        <!-- Discover button -->
        <button class="discover-btn" @click.stop="$emit('hotel-click', hotel)">
          Discover
          <svg width="14" height="14" viewBox="0 0 24 24" fill="none">
            <path
              d="M5 12H19M19 12L12 5M19 12L12 19"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
        </button>
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
          typeof hotel.name === "string" &&
          typeof hotel.location === "string" &&
          typeof hotel.image === "string" &&
          typeof hotel.stars === "number" &&
          typeof hotel.rating === "number" &&
          typeof hotel.currentPrice === "string"
        );
      },
    },
  },
  emits: ["hotel-click"],
};
</script>

<style scoped>
.hotel-card {
  background: white;
  border-radius: 12px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
  overflow: hidden;
  transition: all 0.3s ease;
  cursor: pointer;
  height: 100%;
  display: flex;
  flex-direction: column;
  max-width: 380px;
}

.hotel-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15);
}

.hotel-image-container {
  position: relative;
  height: 220px;
  overflow: hidden;
}

.hotel-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.hotel-card:hover .hotel-image {
  transform: scale(1.05);
}

.free-child-badge {
  position: absolute;
  top: 16px;
  left: 16px;
  background: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 6px 12px;
  border-radius: 6px;
  font-size: 12px;
  font-weight: 500;
  backdrop-filter: blur(4px);
}

.rating-circles-overlay {
  position: absolute;
  top: 16px;
  right: 16px;
  display: flex;
  gap: 4px;
}

.rating-circle {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  transition: background-color 0.2s;
}

.circle-filled {
  background: #ffa500;
}

.circle-empty {
  background: rgba(255, 255, 255, 0.5);
  backdrop-filter: blur(2px);
}

.save-amount {
  position: absolute;
  bottom: 16px;
  left: 16px;
  background: rgba(220, 53, 69, 0.9);
  color: white;
  padding: 6px 12px;
  border-radius: 6px;
  font-size: 12px;
  font-weight: 600;
  backdrop-filter: blur(4px);
}

.hotel-content {
  padding: 20px;
  display: flex;
  flex-direction: column;
  flex: 1;
}

.hotel-name {
  font-size: 20px;
  font-weight: 400;
  color: #333;
  margin: 0 0 8px 0;
  line-height: 1.3;
  font-family: "the-seasons", serif;
  min-height: 50px;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.hotel-location {
  color: #999;
  font-size: 14px;
  line-height: 1.4;
  margin-bottom: 24px;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.hotel-pricing-section {
  margin-top: auto;
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
}

.price-container {
  display: flex;
  flex-direction: column;
}

.price-label {
  font-size: 14px;
  color: #666;
  margin-bottom: 4px;
}

.price-row {
  display: flex;
  align-items: baseline;
  gap: 4px;
}

.price-amount {
  font-size: 24px;
  font-weight: 700;
  color: #333;
  line-height: 1;
}

.price-note {
  font-size: 16px;
  font-weight: 400;
  color: #666;
}

.discover-btn {
  background: transparent;
  color: #ac7872;
  border: none;
  padding: 0;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s;
  display: flex;
  align-items: center;
  gap: 6px;
  text-decoration: none;
}

.discover-btn:hover {
  color: #8b5f5a;
  transform: translateX(2px);
}

.discover-btn svg {
  transition: transform 0.2s;
}

.discover-btn:hover svg {
  transform: translateX(2px);
}

/* Responsive Design */
@media (max-width: 768px) {
  .hotel-card {
    max-width: 100%;
  }

  .hotel-image-container {
    height: 200px;
  }

  .hotel-content {
    padding: 16px;
  }

  .hotel-name {
    font-size: 18px;
    min-height: 44px;
  }

  .hotel-location {
    font-size: 13px;
    margin-bottom: 20px;
  }

  .price-amount {
    font-size: 22px;
  }

  .discover-btn {
    font-size: 13px;
  }
}

@media (max-width: 480px) {
  .hotel-content {
    padding: 14px;
  }

  .hotel-pricing-section {
    flex-direction: column;
    align-items: stretch;
    gap: 12px;
  }

  .discover-btn {
    align-self: flex-end;
  }
}
</style>
