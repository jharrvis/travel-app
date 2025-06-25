<template>
  <div class="hotel-card">
    <!-- BARIS PERTAMA: DETAIL HOTEL - 2 KOLOM (Gambar & Deskripsi) -->
    <div class="hotel-main-section">
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

          <!-- Images Button (sekarang akan dikembalikan, tetapi di kolom harga) -->
          <!-- <div class="images-btn">
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
          </div> -->
        </div>
      </div>

      <!-- KOLOM 2: Hotel Content - Flex Column Layout -->
      <div class="hotel-content">
        <!-- Hotel Name dengan Info Icon -->
        <div class="hotel-title-row">
          <h3 class="hotel-name">{{ hotel.name }}</h3>
          <div
            class="favorite-icon"
            @click.stop="toggleFavorite"
            :class="{ 'is-favorited': isFavorited }"
          >
            <svg
              width="20"
              height="20"
              viewBox="0 0 24 24"
              fill="none"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            >
              <path
                v-if="!isFavorited"
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
            <div
              class="info-tooltip"
              v-if="amenity.includes('Waterpark')"
              @mouseover="
                showTooltip(
                  'This amenity includes a waterpark with various slides and pools suitable for all ages.',
                  $event
                )
              "
              @mouseleave="hideTooltip"
            >
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
      </div>
    </div>

    <!-- SECTION 2: Pricing Info - Updated to 4 columns -->
    <div class="pricing-section">
      <!-- Kolom 1: Images & Map dikembalikan -->
      <div class="pricing-column image-map-column">
        <div class="icon-text-pair" @click.stop="openGalleryModal(0)">
          <svg
            width="24"
            height="24"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
          >
            <rect x="3" y="3" width="18" height="18" rx="2" ry="2"></rect>
            <circle cx="8.5" cy="8.5" r="1.5"></circle>
            <polyline points="21 15 16 10 5 21"></polyline>
          </svg>
          <span class="column-link">Images</span>
        </div>
        <div class="icon-text-pair" @click.stop="openMapModal()">
          <svg
            width="24"
            height="24"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
          >
            <path d="M21 10c0 7-9 13-9 13s-9-6-9-13a9 9 0 0 1 18 0z"></path>
            <circle cx="12" cy="10" r="3"></circle>
          </svg>
          <span class="column-link">Map</span>
        </div>
      </div>

      <!-- Kolom 2: Date -->
      <div class="pricing-column date-column">
        <div class="info-label">DATE</div>
        <div class="info-value">
          {{ formatDate(searchData.startDate) }}
        </div>
      </div>

      <!-- Kolom 3: Nights in Resort -->
      <div class="pricing-column nights-column">
        <div class="info-label">NIGHTS IN RESORT</div>
        <div class="info-value">
          {{ calculateNights(searchData.startDate, searchData.endDate) }}
        </div>
      </div>

      <!-- Kolom 4: Price (Adult & Total) -->
      <div class="pricing-column price-column">
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
              hotel.pricing?.total_price || extractPrice(hotel.currentPrice) * 2
            )
          }}</span>
        </div>
      </div>
    </div>

    <!-- SECTION 3: FLIGHT DETAILS & ROOM DETAILS -->
    <div class="flight-room-section">
      <!-- Flight Details -->
      <div class="flight-detail-row">
        <div class="detail-header-column">Flight details</div>
        <div class="detail-content-column">
          <span class="airline-name">{{
            hotel.flight_details?.airline || "Emirates"
          }}</span>
        </div>
        <div class="detail-content-column baggage-info">
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
          <span>{{ hotel.flight_details?.baggage || "30kgs included" }}</span>
        </div>
        <div class="detail-content-column">
          <span class="flight-class">{{
            hotel.flight_details?.class || "Economy"
          }}</span>
        </div>
        <div class="detail-content-column flight-info-link-wrapper">
          <a href="#" class="flight-info-link" @click.stop="showFlightInfo"
            >Flight information</a
          >
        </div>
      </div>

      <!-- Room Details -->
      <div class="room-detail-row">
        <div class="detail-header-column">Room details</div>
        <div class="detail-content-column">
          <span class="room-type">{{
            currentRoom.roomType || "Palace Superior Room"
          }}</span>
        </div>
        <div class="detail-content-column room-board">
          <span>{{ currentRoom.mealPlan || "Half Board" }}</span>
        </div>
        <div class="detail-content-column change-link-wrapper">
          <a href="#" class="change-link" @click.stop="openRoomModal"
            >Change room & meal plan</a
          >
        </div>
      </div>
    </div>

    <!-- SECTION 4: Deposit and Continue -->
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
        <a href="#" class="deposit-link" @click.stop="showDepositBreakdown"
          >See deposit breakdown</a
        >
        <div
          class="info-icon deposit-info-icon"
          @click.stop="showDepositBreakdown"
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

      <div class="continue-section">
        <button class="continue-btn" @click.stop="openContinueModal()">
          Continue
        </button>
      </div>
    </div>

    <!-- Gallery Modal -->
    <div
      v-if="showGalleryModal"
      class="modal-overlay"
      @click.self="closeGalleryModal"
    >
      <div class="gallery-modal-content">
        <!-- Close Button Revamped -->
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
          :src="hotel.gallery[galleryActiveImageIndex]"
          :alt="hotel.name"
          class="modal-image"
        />
        <div class="gallery-modal-nav-revamped">
          <button
            @click="previousGalleryImage"
            :disabled="hotel.gallery.length <= 1"
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
          <span
            >{{ galleryActiveImageIndex + 1 }} /
            {{ hotel.gallery.length }}</span
          >
          <button
            @click="nextGalleryImage"
            :disabled="hotel.gallery.length <= 1"
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

    <!-- Map Modal -->
    <div v-if="showMapModal" class="modal-overlay" @click.self="closeMapModal">
      <div class="map-modal-content">
        <button class="close-modal-btn-revamped" @click="closeMapModal">
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
        <h3>Location of {{ hotel.name }}</h3>
        <img
          src="https://placehold.co/600x400/CCCCCC/000000?text=Hotel+Location+Map"
          alt="Hotel Location Map"
          class="modal-map-image"
        />
        <p>
          This is a placeholder map. Full interactive map features require an
          API key.
        </p>
      </div>
    </div>

    <!-- Continue Confirmation Modal -->
    <div
      v-if="showContinueModal"
      class="modal-overlay"
      @click.self="closeContinueModal"
    >
      <div class="continue-modal-content">
        <div class="continue-modal-header">
          <h3>Before you continue</h3>
          <button class="close-modal-btn-revamped" @click="closeContinueModal">
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
        </div>
        <div class="continue-modal-body">
          <p>
            There is some important information we would like to share with you
            as you continue your booking.
          </p>
          <div class="baggage-info-box">
            <p>
              Passengers flying aboard Maldivian Seaplanes and domestic flights
              will be entitled to a baggage allowance of 20 kg for checked-in
              luggage and 5 kg for hand luggage. Excess baggage is subject to an
              additional charge of US$5 per kg per way, subject to GST. Infants
              do not have any luggage allowance on board these flights. Costs
              are subject to change without notice. This is not applicable for
              speedboat transfers
            </p>
          </div>
        </div>
        <div class="continue-modal-footer">
          <a href="#" class="go-back-link" @click.stop="closeContinueModal"
            >Go back to hotel selection</a
          >
          <button class="ok-btn" @click.stop="confirmContinue">Ok</button>
        </div>
      </div>
    </div>

    <!-- Rooms & Guests Modal -->
    <div
      v-if="showRoomModal"
      class="modal-overlay"
      @click.self="closeRoomModal"
    >
      <div class="room-modal-content">
        <div class="room-modal-header">
          <h3>Rooms & Guests</h3>
          <button class="close-modal-btn-revamped" @click="closeRoomModal">
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
        </div>
        <div class="room-modal-body">
          <div class="room-item" v-for="(room, index) in rooms" :key="index">
            <h4>Room {{ index + 1 }}</h4>
            <div class="guest-controls">
              <div class="guest-type">
                <span>Adults</span>
                <span class="age-info">Age 18+</span>
              </div>
              <div class="quantity-control">
                <button
                  @click="updateGuest(index, 'adults', -1)"
                  :disabled="room.adults <= 1"
                >
                  -
                </button>
                <span>{{ room.adults }}</span>
                <button @click="updateGuest(index, 'adults', 1)">+</button>
              </div>
            </div>
            <div class="guest-controls">
              <div class="guest-type">
                <span>Children</span>
                <span class="age-info">Age 0-17</span>
              </div>
              <div class="quantity-control">
                <button
                  @click="updateGuest(index, 'children', -1)"
                  :disabled="room.children <= 0"
                >
                  -
                </button>
                <span>{{ room.children }}</span>
                <button @click="updateGuest(index, 'children', 1)">+</button>
              </div>
            </div>
            <div class="meal-plan-selection">
              <label :for="'meal-plan-' + index">Meal Plan:</label>
              <select :id="'meal-plan-' + index" v-model="room.mealPlan">
                <option value="Half Board">Half Board</option>
                <option value="Full Board">Full Board</option>
                <option value="All Inclusive">All Inclusive</option>
                <option value="Bed & Breakfast">Bed & Breakfast</option>
              </select>
            </div>
          </div>
          <button class="add-room-btn" @click="addRoom">
            + Add another room
          </button>
        </div>
        <div class="room-modal-footer">
          <button class="apply-btn" @click="applyRoomChanges">Apply</button>
        </div>
      </div>
    </div>

    <!-- Notification Container -->
    <transition name="fade">
      <div v-if="showNotification" class="notification">
        {{ notificationMessage }}
      </div>
    </transition>

    <!-- Popover for Info Icon -->
    <div v-if="showPopover" class="popover" :style="popoverStyle">
      {{ popoverContent }}
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
      showGalleryModal: false, // New: to control gallery modal visibility
      galleryActiveImageIndex: 0, // New: to track active image in gallery modal
      showMapModal: false, // New: to control map modal visibility
      showContinueModal: false, // New: to control continue confirmation modal
      showRoomModal: false, // New: to control rooms & guests modal visibility
      isFavorited: false, // New: to manage favorite state
      showNotification: false, // New: to control notification visibility
      notificationMessage: "", // New: to store notification message
      showPopover: false, // New: to control popover visibility
      popoverContent: "", // New: to store popover text
      popoverStyle: {}, // New: to control popover position
      rooms: [
        { adults: 2, children: 0, mealPlan: "Half Board" }, // Initial room setup
      ],
      currentRoom: {
        // To reflect selected room details on the card
        roomType: "Palace Superior Room",
        mealPlan: "Half Board",
      },
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
      const numericAmount = parseFloat(amount);
      if (isNaN(numericAmount)) {
        return "£0";
      }
      return `£${numericAmount.toLocaleString("en-GB", {
        minimumFractionDigits: 0,
        maximumFractionDigits: 0,
      })}`;
    },

    extractPrice(priceString) {
      if (typeof priceString === "number") return priceString;
      if (typeof priceString === "string") {
        const match = priceString.match(/[\d,.]+/);
        return match ? parseFloat(match[0].replace(/,/g, "")) : 0;
      }
      return 0;
    },

    // Gallery Modal Methods
    openGalleryModal(initialIndex) {
      this.galleryActiveImageIndex = initialIndex;
      this.showGalleryModal = true;
    },
    closeGalleryModal() {
      this.showGalleryModal = false;
    },
    nextGalleryImage() {
      if (this.hotel.gallery && this.hotel.gallery.length > 1) {
        this.galleryActiveImageIndex =
          (this.galleryActiveImageIndex + 1) % this.hotel.gallery.length;
      }
    },
    previousGalleryImage() {
      if (this.hotel.gallery && this.hotel.gallery.length > 1) {
        this.galleryActiveImageIndex =
          this.galleryActiveImageIndex === 0
            ? this.hotel.gallery.length - 1
            : this.galleryActiveImageIndex - 1;
      }
    },

    // Map Modal Methods
    openMapModal() {
      this.showMapModal = true;
    },
    closeMapModal() {
      this.showMapModal = false;
    },

    // Continue Confirmation Modal Methods
    openContinueModal() {
      this.showContinueModal = true;
    },
    closeContinueModal() {
      this.showContinueModal = false;
    },
    confirmContinue() {
      this.closeContinueModal();
      this.$emit("hotel-click", this.hotel); // Emit event to continue to hotel detail
    },

    // Rooms & Guests Modal Methods
    openRoomModal() {
      this.showRoomModal = true;
      // Initialize rooms array from currentRoom for editing
      this.rooms = [
        {
          adults: this.currentRoom.adults || 2, // Default to 2 adults if not set
          children: this.currentRoom.children || 0, // Default to 0 children if not set
          mealPlan: this.currentRoom.mealPlan || "Half Board",
        },
      ];
    },
    closeRoomModal() {
      this.showRoomModal = false;
    },
    addRoom() {
      this.rooms.push({ adults: 2, children: 0, mealPlan: "Half Board" });
    },
    updateGuest(roomIndex, type, change) {
      if (type === "adults") {
        this.rooms[roomIndex].adults = Math.max(
          1,
          this.rooms[roomIndex].adults + change
        ); // Minimum 1 adult
      } else if (type === "children") {
        this.rooms[roomIndex].children = Math.max(
          0,
          this.rooms[roomIndex].children + change
        ); // Minimum 0 children
      }
    },
    applyRoomChanges() {
      // For simplicity, applying changes to the first room only on the card
      // In a real app, you might aggregate all room data or update a complex object
      if (this.rooms.length > 0) {
        this.currentRoom.roomType = `Room (${this.rooms[0].adults} A, ${this.rooms[0].children} C)`;
        this.currentRoom.mealPlan = this.rooms[0].mealPlan;
      }
      this.closeRoomModal();
      this.showNotificationMessage("Room and meal plan updated successfully.");
    },

    // Favorite button method with notification
    toggleFavorite() {
      this.isFavorited = !this.isFavorited;
      if (this.isFavorited) {
        this.showNotificationMessage(
          "Item has been added to your favorites list."
        );
        // Optional: Revert to outline after a short delay for demonstration
        setTimeout(() => {
          this.isFavorited = false;
        }, 1500); // Revert after 1.5 seconds
      } else {
        this.showNotificationMessage(
          "Item has been removed from your favorites list."
        );
      }
      console.log(
        `Hotel ${this.hotel.name} is now ${
          this.isFavorited ? "favorited" : "unfavorited"
        }`
      );
    },

    // Method to show notification
    showNotificationMessage(message) {
      this.notificationMessage = message;
      this.showNotification = true;
      setTimeout(() => {
        this.showNotification = false;
        this.notificationMessage = "";
      }, 3000); // Hide notification after 3 seconds
    },

    // Popover methods
    showTooltip(content, event) {
      this.popoverContent = content;
      this.showPopover = true;
      // Position popover near the mouse cursor
      this.popoverStyle = {
        top: `${event.clientY + 15}px`, // 15px below cursor
        left: `${event.clientX}px`,
        transform: `translateX(-50%)`, // Center popover horizontally
      };
    },
    hideTooltip() {
      this.showPopover = false;
      this.popoverContent = "";
    },

    showInfo() {
      console.log("Show info for hotel:", this.hotel.name);
    },

    showFlightInfo() {
      console.log("Show flight information");
    },

    showDepositBreakdown() {
      console.log("Show deposit breakdown");
    },
  },
};
</script>

<style scoped>
/* Base Hotel Card Styles - Column Layout */
.hotel-card {
  background: white;
  border: 1px solid #ddd;
  overflow: hidden;
  transition: all 0.3s ease;
  /* Removed cursor: pointer; to prevent card-wide click */
  display: flex;
  flex-direction: column; /* Ensure main container is a column */
  width: 100%;
  border-radius: 8px; /* Added border-radius as per image */
}

.hotel-card:hover {
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
}

/* SECTION 1: Hotel Main Section (Image & Content) */
.hotel-main-section {
  display: flex;
  min-height: 280px;
}

/* KOLOM 1: Image Gallery Section */
.hotel-image-container {
  position: relative;
  width: 320px;
  flex-shrink: 0;
  overflow: hidden;
  border-top-left-radius: 8px; /* Apply to corners matching card */
  border-bottom-left-radius: 8px; /* Apply to corners matching card */
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
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1); /* Added subtle shadow */
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
  top: 8px; /* Sedikit naik */
  left: 8px; /* Sedikit ke kiri */
  background: #dc3545;
  color: white;
  padding: 6px 12px;
  border-radius: 4px;
  font-size: 12px;
  font-weight: 600;
  z-index: 3; /* Pastikan lebih tinggi dari elemen lain di dekatnya */
  box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.2); /* Tambahkan sedikit bayangan untuk efek menonjol */
}

/* .images-btn dihapus sepenuhnya */

/* KOLOM 2: Hotel Content - Flex Column Layout */
.hotel-content {
  flex: 1;
  padding: 20px;
  display: flex;
  flex-direction: column;
  gap: 12px; /* Reduced gap for a tighter look */
}

.hotel-title-row {
  display: flex;
  align-items: center; /* Changed to center for better alignment */
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

/* Favorite Icon Styles */
.favorite-icon {
  background: transparent; /* Changed from blue background */
  color: #333; /* Default color for outline */
  width: 28px; /* Slightly larger for heart icon */
  height: 28px;
  border-radius: 50%;
  border: 1px solid #ccc; /* Outline border */
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  flex-shrink: 0;
  margin-left: 8px;
  transition: all 0.2s ease;
}

.favorite-icon:hover {
  background-color: rgba(220, 53, 69, 0.1); /* Light red on hover */
}

.favorite-icon svg {
  width: 18px; /* Heart icon size */
  height: 18px;
  stroke: #666; /* Default stroke color for heart outline */
  stroke-width: 2;
  fill: none; /* No fill by default for outline */
  transition: all 0.2s ease;
}

.favorite-icon.is-favorited {
  border-color: #dc3545; /* Red border when favorited */
  background-color: rgba(
    220,
    53,
    69,
    0.2
  ); /* Light red background when favorited */
}

.favorite-icon.is-favorited svg {
  fill: #dc3545; /* Red fill when favorited */
  stroke: #dc3545; /* Red stroke when favorited */
}

.hotel-stars {
  color: #ffa500;
  font-size: 16px;
  letter-spacing: 2px; /* Added spacing to stars */
}

.hotel-description {
  color: #333;
  font-size: 14px;
  line-height: 1.5;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
  margin-top: 4px; /* Adjust spacing */
  margin-bottom: 8px; /* Adjust spacing */
}

.hotel-amenities {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.amenity-item {
  position: relative; /* Needed for popover positioning */
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
  color: #333;
}

.check-icon {
  flex-shrink: 0;
  color: #28a745; /* Ensure checkmark color is green */
}

.info-tooltip {
  margin-left: 4px;
  color: #666;
  cursor: pointer;
  display: flex; /* To align the SVG */
  align-items: center;
}

.info-tooltip svg {
  width: 14px; /* Match image size */
  height: 14px; /* Match image size */
  stroke: #6c757d; /* Grey color for the info icon */
  stroke-width: 2;
}

/* SECTION 2: Pricing Info - Updated to 4 columns */
.pricing-section {
  display: flex; /* Use flexbox for columns */
  border-top: 1px solid #eee; /* Existing top border */
  border-bottom: 1px solid #eee; /* Add bottom border for the whole section */
  background-color: white; /* Ensure consistent background */
}

.pricing-column {
  flex: 1; /* Each column takes equal width */
  padding: 16px 0; /* Vertical padding, horizontal padding handled by border-left */
  display: flex;
  flex-direction: column;
  justify-content: center; /* Center content vertically */
  align-items: center; /* Center content horizontally by default */
  border-left: 1px solid #eee; /* Border for each column */
}

/* Remove the first border-left for the very first column */
.pricing-column:first-child {
  border-left: none;
}

.image-map-column {
  /* Disesuaikan untuk menampilkan Images & Map kembali */
  display: flex;
  flex-direction: row; /* Images and Map side by side */
  align-items: center;
  justify-content: center; /* Pusatkan karena ada dua item */
  gap: 15px; /* Kembali ke gap 15px */
  color: #153b3c; /* Updated color for links */
  font-size: 14px;
  font-weight: 600; /* Bold as per image */
}

.icon-text-pair {
  display: flex;
  flex-direction: column; /* Icon above text */
  align-items: center;
  gap: 4px; /* Space between icon and text */
  cursor: pointer; /* Added cursor pointer for clickable icons */
}

.icon-text-pair svg {
  width: 24px; /* Size of icons */
  height: 24px;
  stroke: #153b3c; /* Updated color for icons */
}

.column-link {
  text-decoration: none; /* Remove underline */
}

.column-link:hover {
  text-decoration: underline; /* Add underline on hover */
}

.date-column,
.nights-column {
  /* Already has info-label and info-value, just ensure centering */
  text-align: center;
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

.price-column {
  /* Specific styling for the price column */
  padding-left: 16px; /* Add left padding for content, not just border */
  padding-right: 16px; /* Add right padding */
  align-items: flex-end; /* Align prices to the right */
  justify-content: center;
}

.adult-price {
  display: flex;
  align-items: baseline; /* Align text baselines */
  gap: 5px; /* Space between label and amount */
  font-size: 14px;
  color: #666;
}

.adult-label {
  font-size: 14px; /* Ensure label is not too small */
  color: #666;
}

.adult-amount {
  font-size: 16px; /* Smaller than total */
  font-weight: 600;
  color: #333;
}

.total-price {
  display: flex;
  flex-direction: row; /* Label and amount on same line */
  align-items: baseline;
  gap: 8px; /* Space between label and amount */
  margin-top: 5px; /* Space from adult price */
}

.total-label {
  font-size: 14px;
  color: #666;
  font-weight: 600;
}

.total-amount {
  font-size: 20px;
  font-weight: 700;
  color: #333;
}

/* SECTION 3: FLIGHT DETAILS & ROOM DETAILS - Updated Layout */
.flight-room-section {
  display: flex;
  flex-direction: column; /* Stack rows vertically */
  border-bottom: 1px solid #eee; /* Add bottom border for the whole section */
  background: white; /* Ensure consistent background */
}

.flight-detail-row,
.room-detail-row {
  display: flex;
  align-items: center;
  padding: 12px 20px; /* Consistent padding */
  border-top: 1px solid #eee; /* Top border for each row */
}

.flight-detail-row:first-child, /* No top border for the very first row */
.room-detail-row:first-child {
  border-top: none;
}

.detail-header-column {
  font-size: 14px;
  font-weight: 600;
  color: #333;
  flex-basis: 150px; /* Fixed width for header column */
  flex-shrink: 0;
}

.detail-content-column {
  font-size: 14px;
  color: #333;
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: flex-start;
  gap: 4px;
}

.airline-name,
.flight-class,
.room-type {
  font-weight: 600;
}

.baggage-info {
  flex: 1;
}

.baggage-info svg {
  stroke: #666;
  width: 14px;
  height: 14px;
  flex-shrink: 0;
}

.flight-info-link-wrapper,
.change-link-wrapper {
  flex: 0 0 auto;
  margin-left: auto;
}

.flight-info-link,
.change-link {
  color: #007bff;
  text-decoration: none;
  font-size: 12px;
}

.flight-info-link:hover,
.change-link:hover {
  text-decoration: underline;
}

.room-board {
  color: #666;
  font-size: 14px;
}

/* SECTION 4: Deposit and Continue Section */
.deposit-continue-section {
  display: flex;
  align-items: center;
  padding: 16px 20px;
  border-top: 1px solid #eee;
  justify-content: space-between;
  background-color: white;
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

.deposit-info-icon {
  margin-left: 4px;
}

.continue-section {
  display: flex;
  align-items: center;
  gap: 12px;
}

.continue-btn {
  background: #153b3c;
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
  background: #0e2627;
}

/* Modal Styles */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 9999;
}

/* Close button style for all modals */
.close-modal-btn-revamped {
  position: absolute;
  top: 15px;
  right: 15px;
  background: rgba(255, 255, 255, 0.9);
  border: none;
  border-radius: 50%;
  width: 36px;
  height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  z-index: 10001;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
  transition: background 0.2s ease, transform 0.2s ease;
}

.close-modal-btn-revamped:hover {
  background: white;
  transform: scale(1.05);
}

.close-modal-btn-revamped svg {
  color: #333;
  width: 20px;
  height: 20px;
  stroke-width: 2;
}

/* Gallery Modal specific styles */
.gallery-modal-content {
  background: white;
  border-radius: 8px;
  position: relative;
  max-width: 95%;
  max-height: 95%;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.5);
  padding: 0;
}

.modal-image {
  max-width: 100%;
  max-height: 100%;
  display: block;
  object-fit: contain;
  border-radius: 8px;
  flex-grow: 1;
}

.gallery-modal-nav-revamped {
  position: absolute;
  bottom: 15px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  align-items: center;
  gap: 25px;
  color: white;
  background-color: rgba(0, 0, 0, 0.6);
  padding: 10px 20px;
  border-radius: 50px;
  font-size: 16px;
  font-weight: 500;
  z-index: 10000;
  backdrop-filter: blur(5px);
}

.gallery-modal-nav-revamped button {
  background: none;
  border: none;
  color: white;
  cursor: pointer;
  padding: 5px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  transition: background-color 0.2s ease, transform 0.2s ease;
}

.gallery-modal-nav-revamped button:hover:not(:disabled) {
  background-color: rgba(255, 255, 255, 0.2);
  transform: scale(1.1);
}

.gallery-modal-nav-revamped button:disabled {
  opacity: 0.4;
  cursor: not-allowed;
}

.gallery-modal-nav-revamped svg {
  stroke: white;
  width: 24px;
  height: 24px;
}

/* Map Modal Styles */
.map-modal-content {
  background: white;
  padding: 20px;
  border-radius: 8px;
  position: relative;
  max-width: 95%;
  max-height: 95%;
  width: 700px;
  height: auto;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  align-items: center;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
}

.modal-map-image {
  max-width: 100%;
  height: auto;
  border-radius: 4px;
  margin-top: 15px;
  margin-bottom: 10px;
}

.map-modal-content h3 {
  color: #333;
  margin-bottom: 10px;
}

.map-modal-content p {
  font-size: 12px;
  color: #666;
  text-align: center;
}

/* Continue Confirmation Modal Styles */
.continue-modal-content {
  background: white;
  padding: 25px;
  border-radius: 8px;
  position: relative;
  max-width: 600px;
  width: 90%;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.continue-modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #eee;
  padding-bottom: 15px;
  margin-bottom: 0;
}

.continue-modal-header h3 {
  margin: 0;
  font-size: 20px;
  color: #333;
}

.continue-modal-body {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.continue-modal-body p {
  font-size: 14px;
  color: #555;
  line-height: 1.5;
  margin: 0;
}

.baggage-info-box {
  background-color: #f8f9fa;
  border: 1px solid #dee2e6;
  border-radius: 6px;
  padding: 15px;
  font-size: 13px;
  color: #333;
  line-height: 1.6;
}

.continue-modal-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-top: 1px solid #eee;
  padding-top: 15px;
  margin-top: 0;
}

.go-back-link {
  color: #007bff;
  text-decoration: none;
  font-size: 14px;
}

.go-back-link:hover {
  text-decoration: underline;
}

.ok-btn {
  background: #153b3c;
  color: white;
  border: none;
  padding: 10px 25px;
  border-radius: 6px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.2s;
}

.ok-btn:hover {
  background: #0e2627;
}

/* Notification Styles */
.notification {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  background-color: #333;
  color: white;
  padding: 12px 20px;
  border-radius: 8px;
  font-size: 14px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
  z-index: 10002;
  opacity: 1;
  transition: opacity 0.5s ease-in-out;
}

.notification.fade-enter-active,
.notification.fade-leave-active {
  transition: opacity 0.5s;
}
.notification.fade-enter,
.notification.fade-leave-to {
  opacity: 0;
}

/* Rooms & Guests Modal Styles */
.room-modal-content {
  background: white;
  padding: 25px;
  border-radius: 8px;
  position: relative;
  max-width: 500px;
  width: 90%;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.room-modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #eee;
  padding-bottom: 15px;
  margin-bottom: 0;
}

.room-modal-header h3 {
  margin: 0;
  font-size: 20px;
  color: #333;
}

.room-modal-body {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.room-item {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 15px;
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.room-item h4 {
  margin: 0;
  font-size: 16px;
  color: #333;
}

.guest-controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.guest-type {
  display: flex;
  flex-direction: column;
  font-size: 14px;
  color: #333;
}

.guest-type .age-info {
  font-size: 12px;
  color: #666;
}

.quantity-control {
  display: flex;
  align-items: center;
  gap: 10px;
}

.quantity-control button {
  background: #f0f0f0;
  border: 1px solid #ddd;
  border-radius: 4px;
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  font-size: 18px;
  font-weight: 600;
  color: #333;
  transition: background 0.2s;
}

.quantity-control button:hover:not(:disabled) {
  background: #e0e0e0;
}

.quantity-control button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.quantity-control span {
  font-size: 16px;
  font-weight: 600;
  color: #333;
  min-width: 20px;
  text-align: center;
}

.meal-plan-selection {
  display: flex;
  align-items: center;
  gap: 10px;
}

.meal-plan-selection label {
  font-size: 14px;
  color: #333;
  font-weight: 600;
}

.meal-plan-selection select {
  padding: 8px 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 14px;
  color: #333;
  background-color: white;
  flex-grow: 1;
  appearance: none;
  background-image: url("data:image/svg+xml;charset=US-ASCII,%3Csvg%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20width%3D%22292.4%22%20height%3D%22292.4%22%3E%3Cpath%20fill%3D%22%23333%22%20d%3D%22M287%2069.4a17.6%2017.6%200%200%200-24.8%200L146.2%20185.4%2030.8%2069.4c-6.8-6.8-17.6-6.8-24.4%200-6.8%206.8-6.8%2017.6%200%2024.4l130.4%20130.4c6.8%206.8%2017.6%206.8%2024.4%200l130.4-130.4c6.8-6.8%206.8-17.6%20-.4-24.4z%22%2F%3E%3C%2Fsvg%3E");
  background-repeat: no-repeat;
  background-position: right 8px top 50%;
  background-size: 12px auto;
}

.add-room-btn {
  background: none;
  border: 1px dashed #dc3545;
  color: #dc3545;
  padding: 10px 15px;
  border-radius: 6px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.2s, color 0.2s;
  width: 100%;
}

.add-room-btn:hover {
  background: rgba(220, 53, 69, 0.05);
}

.room-modal-footer {
  border-top: 1px solid #eee;
  padding-top: 15px;
  display: flex;
  justify-content: flex-end;
}

.apply-btn {
  background: #153b3c;
  color: white;
  border: none;
  padding: 12px 25px;
  border-radius: 6px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.2s;
}

.apply-btn:hover {
  background: #0e2627;
}

/* Popover Styles */
.popover {
  position: fixed;
  background-color: #333;
  color: white;
  padding: 8px 12px;
  border-radius: 4px;
  font-size: 12px;
  z-index: 10003;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
  white-space: nowrap;
  pointer-events: none;
}

/* Responsive Design */
@media (max-width: 1024px) {
  .hotel-main-section {
    flex-direction: column;
    min-height: auto;
  }

  .hotel-image-container {
    width: 100%;
    height: 200px;
    border-bottom-left-radius: 0;
    border-top-right-radius: 8px;
  }

  .pricing-section {
    flex-wrap: wrap;
  }

  .pricing-column {
    flex-basis: 50%;
    border-left: none;
    border-bottom: 1px solid #eee;
    padding: 16px;
  }

  .pricing-column:nth-child(odd) {
    border-right: 1px solid #eee;
  }

  .pricing-column:last-child {
    border-bottom: none;
  }

  .image-map-column,
  .date-column,
  .nights-column,
  .price-column {
    align-items: center;
    justify-content: center;
    text-align: center;
  }

  .adult-price,
  .total-price {
    align-items: center;
  }

  /* Responsive for Flight/Room Sections */
  .flight-detail-row,
  .room-detail-row {
    flex-wrap: wrap;
    justify-content: flex-start;
    gap: 8px 16px;
  }

  .detail-header-column {
    flex-basis: 100%;
    margin-bottom: 4px;
  }

  .detail-content-column {
    flex-basis: auto;
  }

  .flight-info-link-wrapper,
  .change-link-wrapper {
    margin-left: 0;
    flex-basis: 100%;
    text-align: left;
  }

  .deposit-continue-section {
    flex-direction: column;
    align-items: stretch;
    gap: 12px;
  }

  .continue-section {
    justify-content: flex-end;
  }

  /* Modal responsive adjustments */
  .gallery-modal-content,
  .map-modal-content,
  .room-modal-content,
  .continue-modal-content {
    padding: 15px;
    width: 95%;
  }

  .gallery-modal-nav-revamped {
    font-size: 14px;
    gap: 15px;
    padding: 8px 15px;
  }
}

@media (max-width: 768px) {
  .hotel-content {
    padding: 16px;
  }

  .pricing-section {
    padding: 16px;
    flex-direction: column;
  }

  .pricing-column {
    flex-basis: 100%;
    border-right: none !important;
    border-bottom: 1px solid #eee;
  }

  .pricing-column:last-child {
    border-bottom: none;
  }

  .flight-room-section {
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
    gap: 12px;
  }
  .modal-image {
    max-height: calc(100vh - 120px);
  }
  .gallery-modal-nav-revamped {
    font-size: 12px;
    gap: 10px;
    padding: 6px 12px;
  }
  .close-modal-btn-revamped {
    width: 32px;
    height: 32px;
    top: 10px;
    right: 10px;
  }
  .close-modal-btn-revamped svg {
    width: 18px;
    height: 18px;
  }
}
</style>
