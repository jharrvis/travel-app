<template>
  <div class="search-filters">
    <!-- Price Toggle -->
    <div class="filter-section">
      <div class="price-toggle">
        <span>Price Per Person</span>
        <div
          class="toggle-switch"
          :class="{ active: priceToggle }"
          @click="$emit('price-toggle', !priceToggle)"
        ></div>
        <span>Total Price</span>
      </div>
    </div>

    <!-- Popular Filters -->
    <div class="filter-section">
      <div class="filter-title" @click="toggleFilterSection('popular')">
        Popular filters
        <svg
          class="arrow"
          :class="{ collapsed: !expandedSections.popular }"
          viewBox="0 0 24 24"
        >
          <path d="M7 10l5 5 5-5z" />
        </svg>
      </div>
      <div
        class="filter-content"
        :class="{ collapsed: !expandedSections.popular }"
      >
        <div class="filter-tags">
          <div
            class="filter-tag"
            :class="{ outline: !filters.fiveStar }"
            @click="toggleFilter('fiveStar')"
          >
            5-star hotel
          </div>
          <div
            class="filter-tag outline"
            :class="{ '': filters.fourPlusRating }"
            @click="toggleFilter('fourPlusRating')"
          >
            4+ Tripadvisor rating
          </div>
          <div
            class="filter-tag outline"
            :class="{ '': filters.beach }"
            @click="toggleFilter('beach')"
          >
            Beach
          </div>
        </div>
      </div>
    </div>

    <!-- Promotions -->
    <div class="filter-section">
      <div class="filter-title" @click="toggleFilterSection('promotions')">
        Promotions
        <svg
          class="arrow"
          :class="{ collapsed: !expandedSections.promotions }"
          viewBox="0 0 24 24"
        >
          <path d="M7 10l5 5 5-5z" />
        </svg>
      </div>
      <div
        class="filter-content"
        :class="{ collapsed: !expandedSections.promotions }"
      >
        <div class="filter-list">
          <div class="filter-item" @click="toggleFilter('exclusive')">
            <div class="filter-item-left">
              <div
                class="filter-checkbox"
                :class="{ checked: filters.exclusive }"
              ></div>
              <span class="filter-label">Exclusive</span>
            </div>
          </div>
          <div class="filter-item" @click="toggleFilter('freeChildStays')">
            <div class="filter-item-left">
              <div
                class="filter-checkbox"
                :class="{ checked: filters.freeChildStays }"
              ></div>
              <span class="filter-label">Free child stays</span>
            </div>
            <div class="filter-count">1</div>
          </div>
        </div>
      </div>
    </div>

    <!-- Star Rating -->
    <div class="filter-section">
      <div class="filter-title" @click="toggleFilterSection('starRating')">
        Star rating
        <svg
          class="arrow"
          :class="{ collapsed: !expandedSections.starRating }"
          viewBox="0 0 24 24"
        >
          <path d="M7 10l5 5 5-5z" />
        </svg>
      </div>
      <div
        class="filter-content"
        :class="{ collapsed: !expandedSections.starRating }"
      >
        <div class="filter-list">
          <div class="filter-item" @click="toggleStarFilter(5)">
            <div class="filter-item-left">
              <div
                class="filter-checkbox"
                :class="{ checked: filters.stars.includes(5) }"
              ></div>
              <span class="filter-label">5-star hotel</span>
            </div>
            <div class="filter-count">{{ getStarCount(5) }}</div>
          </div>
          <div class="filter-item" @click="toggleStarFilter(4)">
            <div class="filter-item-left">
              <div
                class="filter-checkbox"
                :class="{ checked: filters.stars.includes(4) }"
              ></div>
              <span class="filter-label">4-star hotel</span>
            </div>
            <div class="filter-count">{{ getStarCount(4) }}</div>
          </div>
          <div class="filter-item" @click="toggleStarFilter(3)">
            <div class="filter-item-left">
              <div
                class="filter-checkbox"
                :class="{ checked: filters.stars.includes(3) }"
              ></div>
              <span class="filter-label">3-star hotel</span>
            </div>
            <div class="filter-count">{{ getStarCount(3) }}</div>
          </div>
          <div class="filter-item" @click="toggleStarFilter(2)">
            <div class="filter-item-left">
              <div
                class="filter-checkbox"
                :class="{ checked: filters.stars.includes(2) }"
              ></div>
              <span class="filter-label">2-star hotel</span>
            </div>
            <div class="filter-count">1</div>
          </div>
          <div class="filter-item" @click="toggleStarFilter(1)">
            <div class="filter-item-left">
              <div
                class="filter-checkbox"
                :class="{ checked: filters.stars.includes(1) }"
              ></div>
              <span class="filter-label">1-star hotel</span>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Board Basis -->
    <div class="filter-section">
      <div class="filter-title" @click="toggleFilterSection('boardBasis')">
        Board basis
        <svg
          class="arrow"
          :class="{ collapsed: !expandedSections.boardBasis }"
          viewBox="0 0 24 24"
        >
          <path d="M7 10l5 5 5-5z" />
        </svg>
      </div>
      <div
        class="filter-content"
        :class="{ collapsed: !expandedSections.boardBasis }"
      >
        <div class="filter-list">
          <div
            v-for="basis in boardBasisOptions"
            :key="basis"
            class="filter-item"
            @click="toggleBoardBasis(basis)"
          >
            <div class="filter-item-left">
              <div
                class="filter-checkbox"
                :class="{ checked: filters.boardBasis.includes(basis) }"
              ></div>
              <span class="filter-label">{{ basis }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Max Price -->
    <div class="filter-section">
      <div class="filter-title" @click="toggleFilterSection('maxPrice')">
        Max price
        <svg
          class="arrow"
          :class="{ collapsed: !expandedSections.maxPrice }"
          viewBox="0 0 24 24"
        >
          <path d="M7 10l5 5 5-5z" />
        </svg>
      </div>
      <div
        class="filter-content"
        :class="{ collapsed: !expandedSections.maxPrice }"
      >
        <div class="price-slider-container">
          <div class="price-range-label">{{ priceRangeLabel }}</div>
          <div class="slider-wrapper">
            <input
              type="range"
              class="price-slider"
              min="200"
              max="5000"
              :value="filters.maxPrice"
              @input="updateMaxPrice"
              id="priceRange"
            />
            <div class="slider-track"></div>
          </div>
          <div class="price-range-values">
            <span>£200</span>
            <span>{{ priceRangeMax }}</span>
          </div>
        </div>
      </div>
    </div>

    <!-- Popular Resorts -->
    <div class="filter-section">
      <div class="filter-title" @click="toggleFilterSection('popularResorts')">
        Popular resorts
        <svg
          class="arrow"
          :class="{ collapsed: !expandedSections.popularResorts }"
          viewBox="0 0 24 24"
        >
          <path d="M7 10l5 5 5-5z" />
        </svg>
      </div>
      <div
        class="filter-content"
        :class="{ collapsed: !expandedSections.popularResorts }"
      >
        <div class="filter-list">
          <div
            v-for="resort in popularResorts"
            :key="resort.name"
            class="filter-item"
            @click="toggleResort(resort.name)"
          >
            <div class="filter-item-left">
              <div
                class="filter-checkbox"
                :class="{
                  checked: filters.resorts.includes(resort.name),
                }"
              ></div>
              <span class="filter-label">{{ resort.name }}</span>
            </div>
            <div class="filter-count">{{ resort.count }}</div>
          </div>
        </div>
      </div>
    </div>

    <!-- Reset Filters Button -->
    <div class="filter-section">
      <button @click="resetFilters" class="reset-filters-btn">
        Reset All Filters
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "SearchFilters",
  props: {
    filters: {
      type: Object,
      required: true,
    },
    priceToggle: {
      type: Boolean,
      default: false,
    },
    allHotels: {
      type: Array,
      default: () => [],
    },
  },
  emits: ["filter-change", "price-toggle", "reset-filters"],
  data() {
    return {
      // Expanded sections - All open by default
      expandedSections: {
        popular: true,
        promotions: true,
        starRating: true,
        boardBasis: true,
        maxPrice: true,
        popularResorts: true,
      },

      // Filter options
      boardBasisOptions: [
        "All inclusive",
        "Full board",
        "Breakfast and dinner",
        "Breakfast included",
        "Self catering",
        "Room only",
      ],

      popularResorts: [
        { name: "Dubai City", count: 69 },
        { name: "Downtown Dubai", count: 23 },
        { name: "The Palm Jumeirah", count: 22 },
        { name: "Jumeirah Beach", count: 15 },
        { name: "Dubai Marina", count: 13 },
        { name: "Jumeirah Beach Residence", count: 13 },
        { name: "Deira", count: 10 },
        { name: "Bur Dubai", count: 8 },
        { name: "Al Barsha", count: 7 },
        { name: "Jebel Ali", count: 6 },
      ],
    };
  },
  computed: {
    priceRangeLabel() {
      return this.filters.maxPrice == 5000
        ? "Any"
        : `Up to £${this.filters.maxPrice}`;
    },

    priceRangeMax() {
      return this.filters.maxPrice == 5000
        ? "Any"
        : `£${this.filters.maxPrice}`;
    },
  },
  methods: {
    toggleFilterSection(section) {
      this.expandedSections[section] = !this.expandedSections[section];
    },

    toggleFilter(filterName) {
      const newFilters = { ...this.filters };
      newFilters[filterName] = !newFilters[filterName];
      this.$emit("filter-change", newFilters);
    },

    toggleStarFilter(stars) {
      const newFilters = { ...this.filters };
      const index = newFilters.stars.indexOf(stars);
      if (index > -1) {
        newFilters.stars.splice(index, 1);
      } else {
        newFilters.stars.push(stars);
      }
      this.$emit("filter-change", newFilters);
    },

    toggleBoardBasis(basis) {
      const newFilters = { ...this.filters };
      const index = newFilters.boardBasis.indexOf(basis);
      if (index > -1) {
        newFilters.boardBasis.splice(index, 1);
      } else {
        newFilters.boardBasis.push(basis);
      }
      this.$emit("filter-change", newFilters);
    },

    toggleResort(resort) {
      const newFilters = { ...this.filters };
      const index = newFilters.resorts.indexOf(resort);
      if (index > -1) {
        newFilters.resorts.splice(index, 1);
      } else {
        newFilters.resorts.push(resort);
      }
      this.$emit("filter-change", newFilters);
    },

    updateMaxPrice(event) {
      const newFilters = { ...this.filters };
      newFilters.maxPrice = parseInt(event.target.value);
      this.$emit("filter-change", newFilters);
    },

    resetFilters() {
      this.$emit("reset-filters");
    },

    getStarCount(stars) {
      return this.allHotels.filter((hotel) => hotel.stars === stars).length;
    },
  },
};
</script>

<style scoped>
.search-filters {
  width: 100%;
}

.filter-section {
  margin-bottom: 25px;
  border-bottom: 1px solid #f0f0f0;
  padding-bottom: 20px;
}

.filter-section:last-child {
  border-bottom: none;
  margin-bottom: 0;
}

.price-toggle {
  display: flex;
  align-items: center;
  gap: 12px;
  justify-content: center;
  font-size: 14px;
  color: #666;
}

.toggle-switch {
  width: 50px;
  height: 25px;
  background: #ddd;
  border-radius: 25px;
  position: relative;
  cursor: pointer;
  transition: background-color 0.3s;
}

.toggle-switch.active {
  background: #ac7872;
}

.toggle-switch::after {
  content: "";
  position: absolute;
  width: 21px;
  height: 21px;
  background: white;
  border-radius: 50%;
  top: 2px;
  left: 2px;
  transition: 0.3s;
}

.toggle-switch.active::after {
  left: 27px;
}

.filter-title {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-weight: 600;
  cursor: pointer;
  padding: 12px 0;
  color: #333;
  font-size: 16px;
}

.filter-title .arrow {
  width: 16px;
  height: 16px;
  fill: #666;
  transition: transform 0.3s;
}

.filter-title .arrow.collapsed {
  transform: rotate(-90deg);
}

.filter-content {
  max-height: 400px;
  overflow-y: auto;
  transition: max-height 0.3s ease-out;
}

.filter-content.collapsed {
  max-height: 0;
  overflow: hidden;
}

.filter-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin-top: 10px;
}

.filter-tag {
  background: #153b3c;
  color: white;
  padding: 8px 16px;
  border-radius: 20px;
  cursor: pointer;
  font-size: 14px;
  transition: all 0.2s;
  border: 2px solid #153b3c;
}

.filter-tag.outline {
  background: transparent;
  color: #153b3c;
  border: 2px solid #153b3c;
}

.filter-tag:hover {
  transform: translateY(-1px);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
}

.filter-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
  margin-top: 10px;
}

.filter-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  cursor: pointer;
  padding: 8px 0;
  transition: color 0.2s;
}

.filter-item:hover {
  color: #ac7872;
}

.filter-item-left {
  display: flex;
  align-items: center;
  gap: 12px;
  flex: 1;
}

.filter-checkbox {
  width: 18px;
  height: 18px;
  border: 2px solid #ddd;
  border-radius: 4px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.2s;
}

.filter-checkbox.checked {
  background: #ac7872;
  border-color: #ac7872;
  color: white;
}

.filter-checkbox.checked::after {
  content: "✓";
  font-size: 12px;
  font-weight: bold;
}

.filter-label {
  font-size: 14px;
  color: #333;
}

.filter-count {
  font-size: 13px;
  color: #666;
  background: #f8f9fa;
  padding: 2px 8px;
  border-radius: 12px;
}

.price-slider-container {
  padding: 15px 0;
}

.price-range-label {
  text-align: center;
  margin-bottom: 15px;
  font-size: 14px;
  color: #666;
  font-weight: 500;
}

.slider-wrapper {
  position: relative;
  margin-bottom: 15px;
}

.price-slider {
  width: 100%;
  height: 4px;
  background: #ddd;
  border-radius: 2px;
  outline: none;
  -webkit-appearance: none;
  cursor: pointer;
}

.price-slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  width: 18px;
  height: 18px;
  background: #ac7872;
  border-radius: 50%;
  cursor: pointer;
}

.price-slider::-moz-range-thumb {
  width: 18px;
  height: 18px;
  background: #ac7872;
  border-radius: 50%;
  cursor: pointer;
  border: none;
}

.price-range-values {
  display: flex;
  justify-content: space-between;
  font-size: 12px;
  color: #666;
}

.reset-filters-btn {
  width: 100%;
  background: #ac7872;
  color: white;
  border: none;
  padding: 12px 20px;
  border-radius: 8px;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.2s;
  font-size: 14px;
}

.reset-filters-btn:hover {
  background: #8b5f5a;
}

/* Responsive Design */
@media (max-width: 768px) {
  .filter-tags {
    flex-direction: column;
  }

  .filter-tag {
    text-align: center;
    width: 100%;
  }

  .price-toggle {
    flex-direction: column;
    gap: 8px;
  }

  .filter-section {
    margin-bottom: 20px;
    padding-bottom: 15px;
  }
}
</style>
