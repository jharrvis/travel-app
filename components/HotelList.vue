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
      <HotelCard
        v-for="hotel in hotels"
        :key="hotel.id"
        :hotel="hotel"
        @hotel-click="$emit('hotel-click', hotel)"
      />
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
import HotelCard from "@/components/HotelCard.vue";

export default {
  name: "HotelList",
  components: {
    HotelCard,
  },
  props: {
    hotels: {
      type: Array,
      required: true,
      validator(hotels) {
        return Array.isArray(hotels);
      },
    },
    sortBy: {
      type: String,
      default: "recommended",
      validator(value) {
        return ["recommended", "price-low", "price-high", "rating"].includes(
          value
        );
      },
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
}

@media (max-width: 480px) {
  .hotel-grid {
    grid-template-columns: 1fr;
    gap: 12px;
  }
}
</style>
