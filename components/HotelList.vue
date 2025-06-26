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

    <!-- Hotel Container - List View Only -->
    <div class="hotel-container hotel-list-view">
      <!-- Iterate over paginated hotels and insert banner at index 2 -->
      <template v-for="(hotel, index) in paginatedHotels">
        <HotelCard
          :key="hotel.id"
          :hotel="hotel"
          :search-data="searchData"
          @hotel-click="$emit('hotel-click', hotel)"
        />
        <!-- Banner insertion logic -->
        <div
          v-if="index === 2 && currentPage === 1"
          class="banner-container"
          :key="'banner-promo'"
        >
          <div class="banner-content">
            <h2 class="banner-title">
              <span class="gift-icon">🎁</span> Limited Time Offer: Free
              Upgrades!
            </h2>
            <p class="banner-description">
              Book select Dubai holidays and receive a complimentary room
              upgrade and late check-out. Don't miss out!
            </p>
            <button class="banner-button">See Offers</button>
          </div>
        </div>
      </template>
    </div>

    <!-- No Results -->
    <div v-if="paginatedHotels.length === 0" class="no-results">
      <div class="no-results-content">
        <h3>No hotels found</h3>
        <p>Try adjusting your search criteria or filters</p>
        <button @click="$emit('reset-filters')" class="reset-filters-btn">
          Reset All Filters
        </button>
      </div>
    </div>

    <!-- Pagination -->
    <div class="pagination-container" v-if="totalPages > 1">
      <div class="pagination">
        <button
          class="pagination-btn"
          :disabled="currentPage === 1"
          @click="goToPage(currentPage - 1)"
        >
          ‹
        </button>

        <button
          v-for="page in visiblePages"
          :key="page"
          class="pagination-btn"
          :class="{ active: currentPage === page }"
          @click="goToPage(page)"
          :disabled="page === '...'"
        >
          {{ page }}
        </button>

        <button
          class="pagination-btn"
          :disabled="currentPage === totalPages"
          @click="goToPage(currentPage + 1)"
        >
          ›
        </button>
      </div>

      <div class="pagination-info">
        Showing {{ (currentPage - 1) * itemsPerPage + 1 }}-{{
          Math.min(currentPage * itemsPerPage, hotels.length)
        }}
        of {{ hotels.length }} hotels
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
    initialViewMode: {
      type: String,
      default: "list",
      validator(value) {
        return ["grid", "list"].includes(value);
      },
    },
  },
  emits: ["hotel-click", "sort-change", "show-map", "reset-filters"],
  data() {
    return {
      currentPage: 1,
    };
  },
  computed: {
    itemsPerPage() {
      return 10; // Fixed for list view
    },

    resultsTitle() {
      const destination = this.searchData.destination || "hotels";
      const duration = this.getDurationText();
      return `${this.hotels.length} ${destination} holidays found${
        duration ? ` for ${duration}` : ""
      }`;
    },

    paginatedHotels() {
      const start = (this.currentPage - 1) * this.itemsPerPage;
      const end = start + this.itemsPerPage;
      return this.hotels.slice(start, end);
    },

    totalPages() {
      return Math.ceil(this.hotels.length / this.itemsPerPage);
    },

    visiblePages() {
      const pages = [];
      const total = this.totalPages;
      const current = this.currentPage;

      if (total <= 5) {
        for (let i = 1; i <= total; i++) {
          pages.push(i);
        }
      } else {
        if (current <= 3) {
          for (let i = 1; i <= 5; i++) {
            pages.push(i);
          }
          pages.push("...");
          pages.push(total);
        } else if (current >= total - 2) {
          pages.push(1);
          pages.push("...");
          for (let i = total - 4; i <= total; i++) {
            pages.push(i);
          }
        } else {
          pages.push(1);
          pages.push("...");
          for (let i = current - 1; i <= current + 1; i++) {
            pages.push(i);
          }
          pages.push("...");
          pages.push(total);
        }
      }

      return pages;
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

    goToPage(page) {
      if (page >= 1 && page <= this.totalPages && page !== "...") {
        this.currentPage = page;
        // Scroll to top of hotel list when page changes
        this.$nextTick(() => {
          this.$el.scrollIntoView({ behavior: "smooth", block: "start" });
        });
      }
    },
  },

  watch: {
    hotels() {
      // Reset to first page when hotels data changes (e.g., filters applied)
      this.currentPage = 1;
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

/* View Toggle Buttons - Removed */

.map-btn {
  padding: 8px 16px;
  border: 1px solid #ddd;
  border-radius: 6px;
  cursor: pointer;
  font-size: 14px;
  transition: all 0.2s;
  background: white;
}

.map-btn:hover {
  background: #e9ecef;
}

/* Hotel Container Styles */
.hotel-container {
  margin-bottom: 40px;
}

/* List View Only */
.hotel-list-view {
  display: flex;
  flex-direction: column;
  gap: 0; /* Remove gap since cards have their own margins */
}

/* Banner Styles */
.banner-container {
  margin: 0 0 20px;
  padding: 20px;
  border: 1px dashed #ac7872;
  border-radius: 8px;
  background-color: #fdf5f4;
  text-align: center;
}

.banner-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
}

.banner-title {
  font-size: 18px;
  font-weight: 600;
  color: #5a7d7c; /* Darker green for title */
  margin: 0;
  display: flex;
  align-items: center;
  gap: 8px;
}

.gift-icon {
  font-size: 24px;
}

.banner-description {
  font-size: 15px;
  color: #666;
  margin: 0;
  max-width: 600px;
}

.banner-button {
  background: #ac7872; /* Button color */
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 6px;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.2s;
}

.banner-button:hover {
  background: #8b5f5a; /* Darker shade on hover */
}

/* No Results */
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

/* Pagination */
.pagination-container {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  margin-top: 40px;
  padding-top: 30px;
  border-top: 1px solid #eee;
  gap: 15px;
}

.pagination {
  display: flex;
  align-items: center;
  gap: 8px;
}

.pagination-btn {
  width: 36px;
  height: 36px;
  border: 1px solid #ddd;
  background: white;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  font-size: 14px;
  font-weight: 500;
  transition: all 0.3s;
  text-decoration: none;
  color: #666;
}

.pagination-btn:hover:not(:disabled) {
  border-color: #ac7872;
  color: #ac7872;
}

.pagination-btn.active {
  background: #333;
  color: white;
  border-color: #333;
}

.pagination-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.pagination-info {
  font-size: 14px;
  color: #666;
  text-align: left;
}

/* Responsive Design */
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
    flex-wrap: wrap;
    gap: 10px;
  }

  .hotel-list-view {
    gap: 0;
  }

  .pagination-container {
    margin-top: 30px;
    padding-top: 20px;
  }

  .pagination {
    flex-wrap: wrap;
    justify-content: flex-start;
  }

  .pagination-info {
    font-size: 13px;
  }
}

@media (max-width: 480px) {
  .sort-controls {
    flex-direction: column;
    align-items: stretch;
    gap: 12px;
  }

  .sort-controls > * {
    width: 100%;
  }

  .map-btn {
    align-self: center;
    width: auto;
  }
}
</style>
