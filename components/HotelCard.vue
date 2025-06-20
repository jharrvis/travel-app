<template>
  <div class="hotel-card" @click="$emit('hotel-click', hotel)">
    <div class="hotel-image-container">
      <img :src="hotel.image" :alt="hotel.name" class="hotel-image" />

      <!-- Free child stay badge -->
      <div class="free-child-badge" v-if="hotel.freeChildStay">
        Free child stay
      </div>

      <!-- Save amount and rating circles in bottom section -->
      <div class="image-bottom-info">
        <div class="save-amount" v-if="hotel.savings">
          {{ hotel.savings }}
        </div>
        <div class="rating-circles">
          <div
            v-for="i in 5"
            :key="i"
            class="rating-circle"
            :class="
              i <= Math.floor(hotel.rating) ? 'circle-filled' : 'circle-empty'
            "
          ></div>
        </div>
      </div>
    </div>

    <div class="hotel-content">
      <div class="hotel-header">
        <div class="hotel-stars">
          <span v-for="star in hotel.stars" :key="star" class="star">★</span>
        </div>
      </div>

      <h3 class="hotel-name">{{ hotel.name }}</h3>

      <div class="hotel-location">{{ hotel.location }}</div>

      <div class="hotel-separator"></div>

      <div class="hotel-bottom">
        <div class="hotel-pricing">
          <div class="price-label">From</div>
          <div class="current-price">
            {{ hotel.currentPrice }}
            <span class="price-note">{{ hotel.priceNote }}</span>
          </div>
        </div>

        <button class="discover-btn" @click.stop="$emit('hotel-click', hotel)">
          Discover
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
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
}

.hotel-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15);
}

.hotel-image-container {
  position: relative;
  height: 200px;
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
  top: 12px;
  left: 12px;
  background: #28a745;
  color: white;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 11px;
  font-weight: 600;
  text-transform: uppercase;
}

.image-bottom-info {
  position: absolute;
  bottom: 12px;
  left: 12px;
  right: 12px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.save-amount {
  background: rgba(220, 53, 69, 0.9);
  color: white;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 12px;
  font-weight: 600;
}

.rating-circles {
  display: flex;
  gap: 3px;
}

.rating-circle {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  transition: background-color 0.2s;
}

.circle-filled {
  background: #153b3c;
}

.circle-empty {
  background: rgba(255, 255, 255, 0.7);
}

.hotel-content {
  padding: 20px;
  display: flex;
  flex-direction: column;
  flex: 1;
}

.hotel-header {
  margin-bottom: 8px;
}

.hotel-stars {
  color: #ffa500;
  font-size: 14px;
}

.star {
  margin-right: 1px;
}

.hotel-name {
  font-size: 18px;
  font-weight: 600;
  color: #333;
  margin: 0 0 8px 0;
  line-height: 1.3;
  font-family: "the-seasons", serif;
  font-weight: 400;
  min-height: 48px;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.hotel-location {
  color: #666;
  font-size: 13px;
  margin-bottom: 16px;
  line-height: 1.4;
  min-height: 36px;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.hotel-separator {
  height: 1px;
  background: #f0f0f0;
  margin: 16px 0;
}

.hotel-bottom {
  margin-top: auto;
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
  gap: 12px;
}

.hotel-pricing {
  flex: 1;
}

.price-label {
  font-size: 12px;
  color: #666;
  margin-bottom: 4px;
}

.current-price {
  font-size: 24px;
  font-weight: 700;
  color: #333;
  line-height: 1;
}

.price-note {
  font-size: 14px;
  font-weight: 400;
  color: #666;
  margin-left: 4px;
}

.discover-btn {
  background: #153b3c;
  color: white;
  border: none;
  padding: 10px 16px;
  border-radius: 6px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s;
  display: flex;
  align-items: center;
  gap: 6px;
  white-space: nowrap;
  flex-shrink: 0;
}

.discover-btn:hover {
  background: #0f2b2c;
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
  .hotel-image-container {
    height: 180px;
  }

  .hotel-content {
    padding: 16px;
  }

  .hotel-name {
    font-size: 16px;
    min-height: 40px;
  }

  .hotel-location {
    font-size: 12px;
    min-height: 32px;
  }

  .current-price {
    font-size: 20px;
  }

  .discover-btn {
    padding: 8px 12px;
    font-size: 13px;
  }
}

@media (max-width: 480px) {
  .hotel-bottom {
    flex-direction: column;
    align-items: stretch;
    gap: 12px;
  }

  .discover-btn {
    width: 100%;
    justify-content: center;
  }
}
</style>
