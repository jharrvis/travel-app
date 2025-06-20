<template>
  <div class="search-form-component">
    <div class="container">
      <div class="search-form">
        <div class="form-row">
          <!-- BARIS PERTAMA -->
          <!-- Destination input -->
          <div class="form-group destination-wide">
            <label>Destination(s) or Hotel name</label>
            <div class="location-input">
              <svg class="location-icon" viewBox="0 0 24 24">
                <path
                  d="M12 2C8.13 2 5 5.13 5 9c0 5.25 7 13 7 13s7-7.75 7-13c0-3.87-3.13-7-7-7zm0 9.5c-1.38 0-2.5-1.12-2.5-2.5s1.12-2.5 2.5-2.5 2.5 1.12 2.5 2.5-1.12 2.5-2.5 2.5z"
                  fill="currentColor"
                />
              </svg>
              <input
                type="text"
                v-model="searchData.destination"
                placeholder="Enter destination"
                @input="handleDestinationChange"
              />
            </div>
          </div>

          <!-- Departure Airport -->
          <div class="form-group departure-airport">
            <label>Departure airport</label>
            <select
              v-model="searchData.departureAirport"
              @change="emitSearchData"
            >
              <option value="">Any airport</option>
              <option value="LHR">London Heathrow</option>
              <option value="LGW">London Gatwick</option>
              <option value="STN">London Stansted</option>
              <option value="MAN">Manchester</option>
              <option value="BHX">Birmingham</option>
              <option value="LTN">London Luton</option>
              <option value="EDI">Edinburgh</option>
              <option value="GLA">Glasgow</option>
              <option value="NCL">Newcastle</option>
              <option value="LBA">Leeds Bradford</option>
            </select>
          </div>

          <!-- BARIS KEDUA -->
          <!-- Date Range (When) -->
          <div class="form-group when-group">
            <label>When</label>
            <div class="date-range-container">
              <input
                type="date"
                v-model="searchData.startDate"
                :min="minDate"
                @change="handleDateChange"
                placeholder="Start date"
              />
              <span class="date-separator">to</span>
              <input
                type="date"
                v-model="searchData.endDate"
                :min="searchData.startDate || minDate"
                @change="handleDateChange"
                placeholder="End date"
              />
            </div>
          </div>

          <!-- Duration (Auto-calculated) -->
          <div class="form-group duration-group">
            <label>How long</label>
            <input
              type="text"
              :value="durationText"
              readonly
              class="duration-display"
              placeholder="Select dates first"
            />
          </div>

          <!-- Rooms Configuration -->
          <div class="form-group rooms-group">
            <label>Room(s) & Guests</label>
            <div class="rooms-input" @click="toggleRoomsDropdown">
              <input
                type="text"
                :value="roomsDisplayText"
                readonly
                placeholder="Select rooms and guests"
              />
              <svg class="dropdown-arrow" viewBox="0 0 24 24">
                <path d="M7 10l5 5 5-5z" />
              </svg>
            </div>

            <!-- Rooms Dropdown -->
            <div v-if="showRoomsDropdown" class="rooms-dropdown" @click.stop>
              <div class="rooms-header">
                <h4>Rooms & Guests</h4>
                <button @click="closeRoomsDropdown" class="close-btn">×</button>
              </div>

              <!-- Rooms List -->
              <div class="rooms-list">
                <div
                  v-for="(room, index) in searchData.rooms"
                  :key="index"
                  class="room-item"
                >
                  <div class="room-header">
                    <span class="room-title">Room {{ index + 1 }}</span>
                    <button
                      v-if="searchData.rooms.length > 1"
                      @click="removeRoom(index)"
                      class="remove-room-btn"
                    >
                      Remove
                    </button>
                  </div>

                  <!-- Adults -->
                  <div class="guest-control">
                    <div class="guest-info">
                      <span class="guest-type">Adults</span>
                      <span class="guest-description">Age 18+</span>
                    </div>
                    <div class="quantity-controls">
                      <button
                        @click="updateGuestCount(index, 'adults', -1)"
                        :disabled="room.adults <= 1"
                        class="quantity-btn"
                      >
                        -
                      </button>
                      <span class="quantity">{{ room.adults }}</span>
                      <button
                        @click="updateGuestCount(index, 'adults', 1)"
                        :disabled="room.adults >= 4"
                        class="quantity-btn"
                      >
                        +
                      </button>
                    </div>
                  </div>

                  <!-- Children -->
                  <div class="guest-control">
                    <div class="guest-info">
                      <span class="guest-type">Children</span>
                      <span class="guest-description">Age 0-17</span>
                    </div>
                    <div class="quantity-controls">
                      <button
                        @click="updateGuestCount(index, 'children', -1)"
                        :disabled="room.children <= 0"
                        class="quantity-btn"
                      >
                        -
                      </button>
                      <span class="quantity">{{ room.children }}</span>
                      <button
                        @click="updateGuestCount(index, 'children', 1)"
                        :disabled="room.children >= 3"
                        class="quantity-btn"
                      >
                        +
                      </button>
                    </div>
                  </div>

                  <!-- Children Ages (if children > 0) -->
                  <div v-if="room.children > 0" class="children-ages">
                    <label class="ages-label">Ages of children</label>
                    <div class="ages-inputs">
                      <select
                        v-for="(age, childIndex) in room.childrenAges"
                        :key="childIndex"
                        v-model="room.childrenAges[childIndex]"
                        @change="emitSearchData"
                        class="age-select"
                      >
                        <option value="">Age</option>
                        <option
                          v-for="age in 18"
                          :key="age - 1"
                          :value="age - 1"
                        >
                          {{ age - 1 }}
                        </option>
                      </select>
                    </div>
                  </div>
                </div>
              </div>

              <!-- Add Room Button -->
              <div class="add-room-section">
                <button
                  @click="addRoom"
                  :disabled="searchData.rooms.length >= 4"
                  class="add-room-btn"
                >
                  + Add another room
                </button>
              </div>

              <!-- Apply Button -->
              <div class="rooms-footer">
                <button @click="applyRoomsSelection" class="apply-btn">
                  Apply
                </button>
              </div>
            </div>
          </div>

          <!-- Search Button -->
          <div class="form-group search-btn-group">
            <button @click="performSearch" class="search-btn">
              <svg class="search-icon" viewBox="0 0 24 24">
                <path
                  d="M21 21L16.514 16.506M19 10.5C19 15.194 15.194 19 10.5 19C5.806 19 2 15.194 2 10.5C2 5.806 5.806 2 10.5 2C15.194 2 19 5.806 19 10.5Z"
                  stroke="currentColor"
                  stroke-width="2"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                />
              </svg>
              Search
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Backdrop for rooms dropdown -->
    <div
      v-if="showRoomsDropdown"
      class="dropdown-backdrop"
      @click="closeRoomsDropdown"
    ></div>
  </div>
</template>

<script>
export default {
  name: "SearchForm",
  props: {
    initialSearchData: {
      type: Object,
      default: () => ({
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
      }),
    },
  },
  emits: ["search", "search-data-change"],
  data() {
    return {
      searchData: { ...this.initialSearchData },
      showRoomsDropdown: false,
      minDate: this.getTodayDate(),
    };
  },
  computed: {
    durationText() {
      if (!this.searchData.startDate || !this.searchData.endDate) {
        return "";
      }

      const start = new Date(this.searchData.startDate);
      const end = new Date(this.searchData.endDate);
      const diffTime = Math.abs(end - start);
      const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));

      if (diffDays === 0) return "Same day";
      if (diffDays === 1) return "1 night";
      return `${diffDays} nights`;
    },

    roomsDisplayText() {
      const totalRooms = this.searchData.rooms.length;
      const totalAdults = this.searchData.rooms.reduce(
        (sum, room) => sum + room.adults,
        0
      );
      const totalChildren = this.searchData.rooms.reduce(
        (sum, room) => sum + room.children,
        0
      );

      let text = `${totalRooms} Room${
        totalRooms > 1 ? "s" : ""
      } / ${totalAdults} Adult${totalAdults > 1 ? "s" : ""}`;

      if (totalChildren > 0) {
        text += ` / ${totalChildren} Child${totalChildren > 1 ? "ren" : ""}`;
      }

      return text;
    },
  },
  watch: {
    initialSearchData: {
      handler(newData) {
        this.searchData = { ...newData };
      },
      deep: true,
    },
  },
  mounted() {
    // Set default dates (today to 7 days from now)
    if (!this.searchData.startDate) {
      const today = new Date();
      const nextWeek = new Date(today);
      nextWeek.setDate(today.getDate() + 7);

      this.searchData.startDate = this.formatDate(today);
      this.searchData.endDate = this.formatDate(nextWeek);
    }

    // Close dropdown when clicking outside
    document.addEventListener("click", this.handleOutsideClick);
  },
  beforeDestroy() {
    document.removeEventListener("click", this.handleOutsideClick);
  },
  methods: {
    getTodayDate() {
      return new Date().toISOString().split("T")[0];
    },

    formatDate(date) {
      return date.toISOString().split("T")[0];
    },

    handleDestinationChange() {
      this.emitSearchData();
    },

    handleDateChange() {
      // Ensure end date is not before start date
      if (this.searchData.startDate && this.searchData.endDate) {
        const start = new Date(this.searchData.startDate);
        const end = new Date(this.searchData.endDate);

        if (end < start) {
          this.searchData.endDate = this.searchData.startDate;
        }
      }

      this.emitSearchData();
    },

    toggleRoomsDropdown() {
      this.showRoomsDropdown = !this.showRoomsDropdown;
    },

    closeRoomsDropdown() {
      this.showRoomsDropdown = false;
    },

    handleOutsideClick(event) {
      const roomsGroup = event.target.closest(".rooms-group");
      if (!roomsGroup && this.showRoomsDropdown) {
        this.closeRoomsDropdown();
      }
    },

    addRoom() {
      if (this.searchData.rooms.length < 4) {
        this.searchData.rooms.push({
          adults: 2,
          children: 0,
          childrenAges: [],
        });
      }
    },

    removeRoom(index) {
      if (this.searchData.rooms.length > 1) {
        this.searchData.rooms.splice(index, 1);
      }
    },

    updateGuestCount(roomIndex, guestType, change) {
      const room = this.searchData.rooms[roomIndex];
      const newCount = room[guestType] + change;

      if (guestType === "adults") {
        if (newCount >= 1 && newCount <= 4) {
          room.adults = newCount;
        }
      } else if (guestType === "children") {
        if (newCount >= 0 && newCount <= 3) {
          room.children = newCount;
          // Update children ages array
          room.childrenAges = Array(newCount).fill("");
        }
      }
    },

    applyRoomsSelection() {
      this.closeRoomsDropdown();
      this.emitSearchData();
    },

    performSearch() {
      // Validate required fields
      if (!this.searchData.destination.trim()) {
        alert("Please enter a destination");
        return;
      }

      if (!this.searchData.startDate || !this.searchData.endDate) {
        alert("Please select your travel dates");
        return;
      }

      // Check if all children have ages specified
      for (let room of this.searchData.rooms) {
        if (room.children > 0) {
          for (let age of room.childrenAges) {
            if (age === "") {
              alert("Please specify ages for all children");
              return;
            }
          }
        }
      }

      this.$emit("search", this.searchData);
    },

    emitSearchData() {
      this.$emit("search-data-change", this.searchData);
    },
  },
};
</script>

<style scoped>
.search-form-component {
  background: #f8f9fa;
  padding: 20px 0;
  position: relative;
}

.search-form {
  background: white;
  border-radius: 12px;
  padding: 20px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
}

/* Main Grid Layout - 2 Rows */
.form-row {
  display: grid;
  grid-template-columns: 1fr 1fr 2fr 1fr 1.5fr 0.8fr;
  grid-template-rows: auto auto;
  gap: 15px;
  align-items: end;
}

/* BARIS PERTAMA */
.destination-wide {
  grid-column: 1 / 3;
  grid-row: 1;
}

.departure-airport {
  grid-column: 3 / 7;
  grid-row: 1;
}

/* BARIS KEDUA */
.when-group {
  grid-column: 1 / 3;
  grid-row: 2;
}

.duration-group {
  grid-column: 3 / 4;
  grid-row: 2;
}

.rooms-group {
  grid-column: 4 / 6;
  grid-row: 2;
}

.search-btn-group {
  grid-column: 6 / 7;
  grid-row: 2;
}

/* Form Group Base Styles */
.form-group {
  display: flex;
  flex-direction: column;
  position: relative;
}

.form-group label {
  font-weight: 600;
  margin-bottom: 6px;
  color: #333;
  font-size: 14px;
}

/* Input and Select Styles */
.form-group input,
.form-group select {
  padding: 12px 16px;
  border: 2px solid #e1e5e9;
  border-radius: 8px;
  font-size: 16px;
  transition: all 0.3s ease;
  background: white;
  width: 100%;
}

.form-group input:focus,
.form-group select:focus {
  outline: none;
  border-color: #ac7872;
  box-shadow: 0 0 0 3px rgba(172, 120, 114, 0.1);
}

/* Location Input with Icon */
.location-input {
  position: relative;
  display: flex;
  align-items: center;
}

.location-icon {
  position: absolute;
  left: 12px;
  width: 20px;
  height: 20px;
  color: #e74c3c;
  z-index: 1;
  pointer-events: none;
}

.location-input input {
  padding-left: 40px !important;
}

/* Date Range Container */
.date-range-container {
  display: flex;
  align-items: center;
  gap: 12px;
}

.date-range-container input {
  flex: 1;
  min-width: 0;
}

.date-separator {
  color: #666;
  font-weight: 500;
  white-space: nowrap;
}

/* Duration Display */
.duration-display {
  background: #f8f9fa !important;
  cursor: not-allowed;
  color: #666;
}

/* Rooms Configuration */
.rooms-input {
  position: relative;
  cursor: pointer;
}

.rooms-input input {
  cursor: pointer;
  padding-right: 40px;
}

.dropdown-arrow {
  position: absolute;
  right: 12px;
  top: 50%;
  transform: translateY(-50%);
  width: 20px;
  height: 20px;
  fill: #666;
  pointer-events: none;
}

/* Rooms Dropdown */
.rooms-dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  background: white;
  border: 2px solid #e1e5e9;
  border-radius: 12px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.15);
  z-index: 1000;
  margin-top: 8px;
  max-height: 500px;
  overflow-y: auto;
  min-width: 450px;
}

.rooms-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 20px 0;
}

.rooms-header h4 {
  margin: 0;
  font-size: 18px;
  font-weight: 600;
}

.close-btn {
  background: none;
  border: none;
  font-size: 24px;
  cursor: pointer;
  color: #666;
  padding: 0;
  width: 24px;
  height: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.rooms-list {
  padding: 20px;
}

.room-item {
  border: 1px solid #e1e5e9;
  border-radius: 8px;
  padding: 16px;
  margin-bottom: 16px;
}

.room-item:last-child {
  margin-bottom: 0;
}

.room-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.room-title {
  font-weight: 600;
  color: #333;
}

.remove-room-btn {
  background: none;
  border: 1px solid #dc3545;
  color: #dc3545;
  padding: 4px 8px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 12px;
  transition: all 0.2s;
}

.remove-room-btn:hover {
  background: #dc3545;
  color: white;
}

.guest-control {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
}

.guest-info {
  display: flex;
  flex-direction: column;
}

.guest-type {
  font-weight: 500;
  color: #333;
}

.guest-description {
  font-size: 12px;
  color: #666;
}

.quantity-controls {
  display: flex;
  align-items: center;
  gap: 12px;
}

.quantity-btn {
  width: 32px;
  height: 32px;
  border: 1px solid #ddd;
  background: white;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.2s;
  font-weight: 600;
}

.quantity-btn:hover:not(:disabled) {
  border-color: #ac7872;
  color: #ac7872;
}

.quantity-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.quantity {
  font-weight: 600;
  min-width: 20px;
  text-align: center;
}

.children-ages {
  margin-top: 12px;
  padding-top: 12px;
  border-top: 1px solid #e1e5e9;
}

.ages-label {
  font-size: 14px;
  font-weight: 500;
  color: #333;
  margin-bottom: 8px;
  display: block;
}

.ages-inputs {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
}

.age-select {
  flex: 1;
  min-width: 60px;
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 14px;
}

.add-room-section {
  padding: 0 20px 20px;
}

.add-room-btn {
  width: 100%;
  padding: 12px;
  background: none;
  border: 2px dashed #ac7872;
  color: #ac7872;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 500;
  transition: all 0.2s;
}

.add-room-btn:hover:not(:disabled) {
  background: rgba(172, 120, 114, 0.1);
}

.add-room-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.rooms-footer {
  padding: 0 20px 20px;
}

.apply-btn {
  width: 100%;
  padding: 12px;
  background: #153b3c;
  color: white;
  border: none;
  border-radius: 8px;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.2s;
}

.apply-btn:hover {
  background: #0f2b2c;
}

/* Search Button */
.search-btn {
  width: 100%;
  padding: 14px 20px;
  background: linear-gradient(135deg, #ac7872 0%, #8b5f5a 100%);
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  white-space: nowrap;
  min-height: 50px;
}

.search-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 20px rgba(172, 120, 114, 0.4);
}

.search-icon {
  width: 20px;
  height: 20px;
  stroke: currentColor;
  fill: none;
}

.dropdown-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 999;
  background: transparent;
}

/* Responsive Design - Tablet */
@media (max-width: 1024px) {
  .form-row {
    grid-template-columns: 1fr 1fr;
    grid-template-rows: auto auto auto auto;
    gap: 15px;
  }

  .destination-wide {
    grid-column: 1 / 2;
    grid-row: 1;
  }

  .departure-airport {
    grid-column: 2 / 3;
    grid-row: 1;
  }

  .when-group {
    grid-column: 1 / 3;
    grid-row: 2;
  }

  .duration-group {
    grid-column: 1 / 2;
    grid-row: 3;
  }

  .rooms-group {
    grid-column: 2 / 3;
    grid-row: 3;
  }

  .search-btn-group {
    grid-column: 1 / 3;
    grid-row: 4;
  }

  .search-btn {
    max-width: 300px;
    margin: 0 auto;
  }
}

/* Responsive Design - Mobile */
@media (max-width: 768px) {
  .search-form {
    padding: 15px;
    margin: 0 10px;
  }

  .form-row {
    grid-template-columns: 1fr;
    grid-template-rows: repeat(6, auto);
    gap: 12px;
  }

  .destination-wide,
  .departure-airport,
  .when-group,
  .duration-group,
  .rooms-group,
  .search-btn-group {
    grid-column: 1;
  }

  .destination-wide {
    grid-row: 1;
  }
  .departure-airport {
    grid-row: 2;
  }
  .when-group {
    grid-row: 3;
  }
  .duration-group {
    grid-row: 4;
  }
  .rooms-group {
    grid-row: 5;
  }
  .search-btn-group {
    grid-row: 6;
  }

  .search-btn {
    width: 100%;
    max-width: none;
  }

  .date-range-container {
    flex-direction: column;
    gap: 8px;
    align-items: stretch;
  }

  .date-separator {
    align-self: center;
  }

  .rooms-dropdown {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 90vw;
    max-width: 450px;
    max-height: 80vh;
    min-width: unset;
  }
}
</style>
