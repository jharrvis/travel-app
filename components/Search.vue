<template>
  <div class="search-area">
    <!-- Search Form Component -->
    <SearchForm
      :initial-search-data="searchFormData"
      @search="handleSearch"
      @search-data-change="handleSearchDataChange"
    />

    <!-- Content Wrapper -->
    <div class="container">
      <div class="content-wrapper">
        <!-- Sidebar with Filters -->
        <div class="sidebar">
          <SearchFilters
            :filters="filters"
            :price-toggle="priceToggle"
            :all-hotels="hotels"
            @filter-change="handleFilterChange"
            @price-toggle="handlePriceToggle"
            @reset-filters="resetFilters"
          />
        </div>

        <!-- Hotel Results -->
        <div class="main-content">
          <HotelList
            :hotels="sortedHotels"
            :sort-by="sortBy"
            :search-data="searchFormData"
            @hotel-click="viewDeal"
            @sort-change="handleSortChange"
            @show-map="showMap"
            @reset-filters="resetFilters"
          />
        </div>
      </div>
    </div>

    <!-- Recently Viewed Hotels -->
    <RecentlyViewed />
  </div>
</template>

<script>
import SearchForm from "@/components/SearchForm.vue";
import SearchFilters from "@/components/SearchFilters.vue";
import HotelList from "@/components/HotelList.vue";

export default {
  name: "SearchComponent",
  components: {
    SearchForm,
    SearchFilters,
    HotelList,
  },
  data() {
    return {
      // Search form data
      searchFormData: {
        destination: "Dubai",
        departureAirport: "",
        startDate: "",
        endDate: "",
        rooms: [
          {
            adults: 2,
            children: 0,
            childrenAges: [],
          },
        ],
      },

      sortBy: "recommended",
      priceToggle: false,
      hotels: [],
      filteredHotels: [],

      // Filter states
      filters: {
        fiveStar: true,
        fourPlusRating: false,
        beach: false,
        exclusive: false,
        freeChildStays: false,
        topPick: false,
        stars: [5],
        boardBasis: [],
        maxPrice: 5000,
        resorts: ["Jumeirah Beach"],
        holidayTypes: [],
        tripadvisorScore: "any",
        facilities: [],
        pools: [],
        kids: [],
      },
    };
  },

  async mounted() {
    try {
      const response = await fetch("/data/search-results.json");
      const data = await response.json();
      console.log("Loaded data:", data); // Debug log

      // ✅ FIX: Menggunakan data.search_results.hotels
      this.hotels = data.search_results.hotels.map((hotel) => ({
        ...hotel,
        freeChildStay: hotel.freeChildStay || Math.random() > 0.7, // Use existing or random
      }));

      console.log("Mapped hotels:", this.hotels); // Debug log
      this.applyFilters();
    } catch (error) {
      console.error("Failed to load hotels:", error);
      this.hotels = [];
      this.filteredHotels = [];
    }
  },

  computed: {
    sortedHotels() {
      const hotels = [...this.filteredHotels];

      switch (this.sortBy) {
        case "price-low":
          return hotels.sort((a, b) => {
            // ✅ FIX: Support both new and old price structure
            const priceA =
              a.pricing?.adult_price ||
              parseInt((a.currentPrice || "£0").replace(/[£,]/g, ""));
            const priceB =
              b.pricing?.adult_price ||
              parseInt((b.currentPrice || "£0").replace(/[£,]/g, ""));
            return priceA - priceB;
          });
        case "price-high":
          return hotels.sort((a, b) => {
            const priceA =
              a.pricing?.adult_price ||
              parseInt((a.currentPrice || "£0").replace(/[£,]/g, ""));
            const priceB =
              b.pricing?.adult_price ||
              parseInt((b.currentPrice || "£0").replace(/[£,]/g, ""));
            return priceB - priceA;
          });
        case "rating":
          return hotels.sort((a, b) => b.stars - a.stars);
        default:
          // ✅ FIX: Sort by top_pick first, then by rating
          return hotels.sort((a, b) => {
            if (a.top_pick && !b.top_pick) return -1;
            if (!a.top_pick && b.top_pick) return 1;
            return b.rating - a.rating;
          });
      }
    },
  },

  methods: {
    // Search Form Handlers
    handleSearch(searchData) {
      console.log("Performing search with:", searchData);
      this.searchFormData = { ...searchData };
      this.applyFilters();
    },

    handleSearchDataChange(searchData) {
      this.searchFormData = { ...searchData };
    },

    // Filter Handlers
    handleFilterChange(newFilters) {
      this.filters = { ...newFilters };
      this.applyFilters();
    },

    handlePriceToggle(showTotalPrice) {
      this.priceToggle = showTotalPrice;
      console.log("Price toggle:", showTotalPrice);
    },

    // Sort Handler
    handleSortChange(newSortBy) {
      this.sortBy = newSortBy;
    },

    applyFilters() {
      let filtered = [...this.hotels];

      // Search query filter
      if (
        this.searchFormData.destination &&
        this.searchFormData.destination.trim()
      ) {
        const query = this.searchFormData.destination.toLowerCase();
        filtered = filtered.filter(
          (hotel) =>
            hotel.name.toLowerCase().includes(query) ||
            hotel.location.toLowerCase().includes(query) ||
            hotel.category.toLowerCase().includes(query)
        );
      }

      // Star rating filter
      if (this.filters.stars.length > 0) {
        filtered = filtered.filter((hotel) =>
          this.filters.stars.includes(hotel.stars)
        );
      }

      // ✅ FIX: Board basis filter - support new structure
      if (this.filters.boardBasis.length > 0) {
        filtered = filtered.filter((hotel) =>
          this.filters.boardBasis.includes(
            hotel.board_basis || hotel.boardBasis
          )
        );
      }

      // ✅ FIX: Price filter - support new pricing structure
      if (this.filters.maxPrice < 5000) {
        filtered = filtered.filter((hotel) => {
          const price =
            hotel.pricing?.adult_price ||
            parseInt((hotel.currentPrice || "£0").replace(/[£,]/g, ""));
          return price <= this.filters.maxPrice;
        });
      }

      // Resort filter
      if (this.filters.resorts.length > 0) {
        filtered = filtered.filter((hotel) => {
          return this.filters.resorts.some((resort) =>
            hotel.location.toLowerCase().includes(resort.toLowerCase())
          );
        });
      }

      // ✅ FIX: Beach filter - check amenities too
      if (this.filters.beach) {
        filtered = filtered.filter(
          (hotel) =>
            hotel.category.toLowerCase().includes("beach") ||
            hotel.location.toLowerCase().includes("beach") ||
            (hotel.amenities &&
              hotel.amenities.some((amenity) =>
                amenity.toLowerCase().includes("beach")
              ))
        );
      }

      // Four plus rating filter
      if (this.filters.fourPlusRating) {
        filtered = filtered.filter((hotel) => hotel.rating >= 4.0);
      }

      // Five star filter
      if (this.filters.fiveStar) {
        filtered = filtered.filter((hotel) => hotel.stars === 5);
      }

      // Free child stays filter
      if (this.filters.freeChildStays) {
        filtered = filtered.filter((hotel) => hotel.freeChildStay);
      }

      // ✅ FIX: Top pick filter
      if (this.filters.topPick) {
        filtered = filtered.filter((hotel) => hotel.top_pick);
      }

      this.filteredHotels = filtered;
      console.log("Filtered hotels:", this.filteredHotels); // Debug log
    },

    resetFilters() {
      this.filters = {
        fiveStar: false,
        fourPlusRating: false,
        beach: false,
        exclusive: false,
        freeChildStays: false,
        topPick: false,
        stars: [],
        boardBasis: [],
        maxPrice: 5000,
        resorts: [],
        holidayTypes: [],
        tripadvisorScore: "any",
        facilities: [],
        pools: [],
        kids: [],
      };
      this.applyFilters();
    },

    showMap() {
      alert("Map view would be implemented here");
    },

    viewDeal(hotel) {
      // Navigate to hotel detail page
      this.$router.push(`/hotel-detail?hotelId=${hotel.id}`);
    },
  },
};
</script>

<style scoped>
.search-area {
  padding: 0;
  min-height: 100vh;
  margin-top: 110px;
}

.content-wrapper {
  display: flex;
  gap: 30px;
  padding: 30px 0;
  align-items: flex-start;
}

.sidebar {
  width: 280px;
  flex-shrink: 0;
  height: fit-content;
  position: sticky;
  top: 20px;
}

.main-content {
  flex: 1;
  min-width: 0;
}

/* Responsive Design */
@media (max-width: 1200px) {
  .search-area {
    margin-top: 70px;
  }

  .content-wrapper {
    flex-direction: column;
    gap: 20px;
  }

  .sidebar {
    width: 100%;
    position: static;
    margin-bottom: 20px;
  }

  .main-content {
    width: 100%;
  }
}

@media (max-width: 768px) {
  .container {
    padding: 0 15px;
  }

  .content-wrapper {
    padding: 20px 0;
    gap: 15px;
  }

  .sidebar,
  .main-content {
    border-radius: 8px;
  }
}

@media (max-width: 480px) {
  .container {
    padding: 0 10px;
  }

  .sidebar,
  .main-content {
    border-radius: 8px;
  }
}
</style>
