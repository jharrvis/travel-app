<template>
  <div class="hotel-list">
    <!-- Sort Controls -->
    <div class="results-header">
      <h1 class="results-title">{{ resultsTitle }}</h1>
      <div class="sort-controls">
        <span>Sort by</span>
        <select
          :value="sortBy"
          @change="$emit('sort-change', $event.target.value)"
          class="sort-dropdown"
        >
          <option value="recommended">Most popular</option>
          <option value="price-low">Price (low to high)</option>
          <option value="price-high">Price (high to low)</option>
          <option value="rating">Star rating</option>
        </select>
        <button class="map-btn" @click="$emit('show-map')">🗺️ Map</button>
      </div>
    </div>

    <!-- Hotel Grid -->
    <div class="hotel-grid">
      <div
        v-for="hotel in hotels"
        :key="hotel.id"
        class="hotel-card"
        @click="$emit('hotel-click', hotel)"
      >
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
                  i <= Math.floor(hotel.rating)
                    ? 'circle-filled'
                    : 'circle-empty'
                "
              ></div>
            </div>
          </div>
        </div>

        <div class="hotel-content">
          <div class="hotel-header">
            <div class="hotel-stars">
              <span v-for="star in hotel.stars" :key="star" class="star"
                >★</span
              >
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

            <button
              class="discover-btn"
              @click.stop="$emit('hotel-click', hotel)"
            >
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
    </div>

    <!-- No Results -->
    <div v-if="hotels.length === 0" class="no-results">
      <div class="no-results-content">
        <h3>No hotels found</h3>
        <p>Try adjusting your search criteria or filters</p>
        <button @click="$emit('reset-filters')" class="reset-filters-btn">
          Reset All Filters
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "HotelList",
  props: {
    hotels: {
      type: Array,
      required: true,
    },
    sortBy: {
      type: String,
      default: "recommended",
    },
    searchData: {
      type: Object,
      default: () => ({}),
    },
  },
  emits: ["hotel-click", "sort-change", "show-map", "reset-filters"],
  computed: {
    resultsTitle() {
      const destination = this.searchData.destination || "hotels";
      const duration = this.getDurationText();
      return `${this.hotels.length} ${destination} holidays found${
        duration ? ` for ${duration}` : ""
      }`;
    },
  },
  methods: {
    getDurationText() {
      if (!this.searchData.startDate || !this.searchData.endDate) {
        return "";
      }

      const start = new Date(this.searchData.startDate);
      const end = new Date(this.searchData.endDate);
      const diffTime = Math.abs(end - start);
      const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));

      if (diffDays === 0) return "same day";
      if (diffDays === 1) return "1 night";
      return `${diffDays} nights`;
    },
  },
};
</script>

<style scoped>
.hotel-list {
  width: 100%;
}

.results-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 0;
  border-bottom: 1px solid #eee;
  margin-bottom: 30px;
}

.results-title {
  font-size: 24px;
  font-weight: 600;
  color: #333;
  margin: 0;
}

.sort-controls {
  display: flex;
  align-items: center;
  gap: 15px;
}

.sort-controls span {
  color: #666;
  font-weight: 500;
  font-size: 14px;
}

.sort-dropdown {
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 6px;
  background: white;
  cursor: pointer;
  font-size: 14px;
}

.map-btn {
  padding: 8px 16px;
  border: 1px solid #ddd;
  border-radius: 6px;
  cursor: pointer;
  font-size: 14px;
  transition: all 0.2s;
}

.map-btn:hover {
  background: #e9ecef;
}

.hotel-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
  gap: 24px;
  margin-bottom: 40px;
}

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

.no-results {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 300px;
  text-align: center;
}

.no-results-content {
  padding: 40px;
}

.no-results-content h3 {
  font-size: 24px;
  color: #333;
  margin-bottom: 16px;
}

.no-results-content p {
  color: #666;
  margin-bottom: 24px;
  font-size: 16px;
}

.reset-filters-btn {
  background: #ac7872;
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 8px;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.2s;
}

.reset-filters-btn:hover {
  background: #8b5f5a;
}

/* Responsive Design */
@media (max-width: 1200px) {
  .hotel-grid {
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 20px;
  }
}

@media (max-width: 768px) {
  .results-header {
    flex-direction: column;
    gap: 15px;
    align-items: flex-start;
    padding: 15px 0;
  }

  .results-title {
    font-size: 20px;
  }

  .sort-controls {
    width: 100%;
    justify-content: space-between;
  }

  .hotel-grid {
    grid-template-columns: 1fr;
    gap: 16px;
  }

  /*  .hotel-card {
    margin: 0 auto;
    max-width: 400px;
  }
  */

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
  .hotel-grid {
    grid-template-columns: 1fr;
    gap: 12px;
  }

  .hotel-card {
    max-width: 100%;
  }

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
