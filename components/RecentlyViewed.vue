<template>
  <div class="recently-viewed">
    <div class="container">
      <div class="recently-viewed-inner">
        <div class="recently-viewed-header">
          <h2 class="recently-viewed-title">Recently viewed hotels</h2>
          <div class="carousel-navigation">
            <button
              class="carousel-btn prev-btn"
              @click="previousSlide"
              :disabled="currentSlide === 0"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 24 24"
                fill="currentColor"
                class="arrow-icon"
              >
                <path d="M15.41 7.41L14 6l-6 6 6 6 1.41-1.41L10.83 12z" />
              </svg>
            </button>
            <button
              class="carousel-btn next-btn"
              @click="nextSlide"
              :disabled="currentSlide >= maxSlides"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 24 24"
                fill="currentColor"
                class="arrow-icon"
              >
                <path d="M8.59 16.59L10 18l6-6-6-6-1.41 1.41L13.17 12z" />
              </svg>
            </button>
          </div>
        </div>

        <div class="carousel-container">
          <div
            class="carousel-wrapper"
            :style="{
              transform: `translateX(-${currentSlide * itemWidthWithGap}px)`,
            }"
          >
            <div
              v-for="hotel in recentlyViewedHotels"
              :key="hotel.id"
              class="recently-viewed-item"
              @click="viewHotel(hotel)"
            >
              <img
                :src="hotel.image"
                :alt="hotel.name"
                class="recently-viewed-image"
              />
              <div class="recently-viewed-content">
                <div class="recently-viewed-stars">
                  {{ generateStars(hotel.stars) }}
                </div>
                <h3 class="recently-viewed-name">{{ hotel.name }}</h3>
                <p class="recently-viewed-location">{{ hotel.location }}</p>
                <div class="recently-viewed-rating">
                  <div class="recently-viewed-circles">
                    <div
                      v-for="n in Math.floor(hotel.rating)"
                      :key="n"
                      class="recently-viewed-circle"
                    ></div>
                  </div>
                  <span class="recently-viewed-reviews"
                    >{{ hotel.reviews.toLocaleString() }} reviews</span
                  >
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "RecentlyViewed",
  data() {
    return {
      currentSlide: 0,
      itemWidth: 0, // This will be calculated dynamically
      gap: 20, // Gap between items
      itemsPerSlideDesktop: 3, // Number of items to display on desktop
      recentlyViewedHotels: [
        {
          id: 1,
          name: "Radisson Beach Resort Palm Jumeirah",
          location: "The Palm Jumeirah, Dubai, United Arab Emirates",
          stars: 4,
          rating: 4.2,
          reviews: 1104,
          image:
            "https://images.unsplash.com/photo-1566073771259-6a8506099945?w=280&h=140&fit=crop",
        },
        {
          id: 2,
          name: "Dukes The Palm, a Royal Hideaway Hotel",
          location: "The Palm Jumeirah, Dubai, United Arab Emirates",
          stars: 5,
          rating: 4.6,
          reviews: 4414,
          image:
            "https://images.unsplash.com/photo-1582719508461-905c673771fd?w=280&h=140&fit=crop",
        },
        {
          id: 3,
          name: "Ibis London Heathrow Airport",
          location: "London, England, United Kingdom",
          stars: 3,
          rating: 4.0,
          reviews: 6345,
          image:
            "https://images.unsplash.com/photo-1551882547-ff40c63fe5fa?w=280&h=140&fit=crop",
        },
        {
          id: 4,
          name: "The Biltmore Hotel Villas",
          location: "Al Barsha, Dubai, United Arab Emirates",
          stars: 5,
          rating: 4.8,
          reviews: 44,
          image:
            "https://images.unsplash.com/photo-1564501049412-61c2a3083791?w=280&h=140&fit=crop",
        },
        {
          id: 5,
          name: "Four Seasons Resort Dubai",
          location: "Jumeirah Beach, Dubai, United Arab Emirates",
          stars: 5,
          rating: 4.7,
          reviews: 2187,
          image:
            "https://images.unsplash.com/photo-1584132967334-10e028bd69f7?w=280&h=140&fit=crop",
        },
        {
          id: 6,
          name: "Atlantis The Palm",
          location: "The Palm Jumeirah, Dubai, United Arab Emirates",
          stars: 5,
          rating: 4.3,
          reviews: 8234,
          image:
            "https://images.unsplash.com/photo-1571896349842-33c89424de2d?w=280&h=140&fit=crop",
        },
      ],
    };
  },

  computed: {
    itemWidthWithGap() {
      return this.itemWidth + this.gap;
    },
    maxSlides() {
      // For desktop, we want 3 items visible, so the max slides are total items - 3
      if (window.innerWidth >= 1024) {
        // Assuming 1024px as desktop breakpoint
        return Math.max(
          0,
          this.recentlyViewedHotels.length - this.itemsPerSlideDesktop
        );
      } else {
        // For smaller screens, revert to previous logic or adjust as needed
        return Math.max(0, this.recentlyViewedHotels.length - 1); // For example, 1 item at a time for mobile/tablet
      }
    },
  },

  mounted() {
    this.calculateItemWidth();
    window.addEventListener("resize", this.calculateItemWidth);
  },

  beforeUnmount() {
    // Changed from beforeDestroy to beforeUnmount for Vue 3 compatibility
    window.removeEventListener("resize", this.calculateItemWidth);
  },

  methods: {
    generateStars(stars) {
      return "★".repeat(stars);
    },

    viewHotel(hotel) {
      alert(`Viewing details for ${hotel.name}`);
      // Here you would typically navigate to hotel details
      // this.$router.push(`/hotel/${hotel.id}`)
    },

    previousSlide() {
      if (this.currentSlide > 0) {
        this.currentSlide--;
      }
    },

    nextSlide() {
      if (this.currentSlide < this.maxSlides) {
        this.currentSlide++;
      }
    },

    calculateItemWidth() {
      const carouselContainer = this.$el.querySelector(".carousel-container");
      if (carouselContainer) {
        const containerWidth = carouselContainer.offsetWidth;

        if (window.innerWidth >= 1024) {
          // Desktop
          // Calculate item width for 3 items with gaps
          this.itemWidth =
            (containerWidth - this.gap * (this.itemsPerSlideDesktop - 1)) /
            this.itemsPerSlideDesktop;
        } else if (window.innerWidth >= 768) {
          // Tablet
          // Display 2 items
          this.itemWidth = (containerWidth - this.gap) / 2;
        } else {
          // Mobile
          // Display 1 item
          this.itemWidth = containerWidth;
        }
      }
    },
  },
};
</script>

<style scoped>
/* Ensure the container for the carousel handles overflow */
.carousel-container {
  overflow: hidden;
  position: relative; /* Needed for absolute positioning of arrows if desired */
}

.carousel-wrapper {
  display: flex;
  transition: transform 0.3s ease;
  gap: 20px; /* This creates the gap between items */
}

.recently-viewed-item {
  flex: 0 0 auto; /* Prevent items from shrinking */
  width: var(--item-width); /* Use CSS variable for dynamic width */
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  box-sizing: border-box; /* Include padding and border in the element's total width and height */
}

/* Set item width dynamically using CSS variable */
.recently-viewed-item {
  width: v-bind(itemWidth + "px");
}

.recently-viewed-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.15);
}

.carousel-btn {
  background: none;
  border: 1px solid; /* Set default border for both */
  border-radius: 50%;
  width: 40px; /* Adjust size as needed */
  height: 40px; /* Adjust size as needed */
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  padding: 0;
  color: #666; /* Default color for disabled/inactive */
}

.carousel-btn.prev-btn {
  border-color: #ddd; /* Light grey for previous */
  color: #666; /* Grey arrow for previous */
}

.carousel-btn.next-btn {
  border-color: #793444; /* Dark red for next */
  color: #793444; /* Dark red arrow for next */
}

.carousel-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  border-color: #ddd !important; /* Ensure disabled state overrides active colors */
  color: #666 !important;
}

.carousel-btn:disabled:hover {
  border-color: #ddd;
  color: #666;
}

.arrow-icon {
  width: 24px; /* Size of the SVG icon */
  height: 24px; /* Size of the SVG icon */
  fill: currentColor; /* Use the parent's color */
}

/* Responsive adjustments */
@media (max-width: 1023px) {
  .recently-viewed-item {
    /* When not desktop, let calculateItemWidth handle the width */
    width: v-bind(itemWidth + "px");
  }
}
@media (max-width: 767px) {
  .recently-viewed-item {
    width: v-bind(itemWidth + "px");
  }
}
</style>
