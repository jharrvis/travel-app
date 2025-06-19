<template>
  <div class="search-area">
    <!-- Search Form -->
    <div class="container">
      <div class="search-form">
        <div class="form-row">
          <!-- Destination input -->
          <div class="form-group destination-wide">
            <label>Destination(s) or Hotel name</label>
            <div class="location-input">
              <input
                type="text"
                v-model="searchQuery"
                placeholder="Enter destination"
                @input="filterHotels"
              />
            </div>
          </div>

          <!-- Departure Airport -->
          <div class="form-group">
            <label>Departure airport</label>
            <select v-model="departureAirport">
              <option value="">Any airport</option>
              <option value="LHR">London Heathrow</option>
              <option value="LGW">London Gatwick</option>
              <option value="MAN">Manchester</option>
              <option value="BHX">Birmingham</option>
            </select>
          </div>

          <!-- When (Date Range) -->
          <div class="form-group">
            <label>When</label>
            <input
              type="date"
              v-model="departureDate"
              placeholder="Start date"
            />
          </div>

          <!-- How Long -->
          <div class="form-group">
            <label>How long</label>
            <select v-model="duration">
              <option value="">Select duration</option>
              <option value="3 nights">3 nights</option>
              <option value="5 nights">5 nights</option>
              <option value="7 nights">7 nights</option>
              <option value="10 nights">10 nights</option>
              <option value="14 nights">14 nights</option>
            </select>
          </div>

          <!-- Rooms -->
          <div class="form-group">
            <label>Room(s)</label>
            <select v-model="rooms">
              <option value="1 Room / 2 Adults">1 Room / 2 Adults</option>
              <option value="1 Room / 1 Adult">1 Room / 1 Adult</option>
              <option value="2 Rooms / 4 Adults">2 Rooms / 4 Adults</option>
              <option value="2 Rooms / Family">2 Rooms / Family</option>
            </select>
          </div>
        </div>
      </div>
    </div>

    <!-- Results Header -->
    <div class="container">
      <div class="results-header">
        <h1 class="results-title">{{ resultsTitle }}</h1>
        <div class="sort-controls">
          <span>Sort by</span>
          <select v-model="sortBy" @change="sortHotels" class="sort-dropdown">
            <option value="recommended">Most popular</option>
            <option value="price-low">Price (low to high)</option>
            <option value="price-high">Price (high to low)</option>
            <option value="rating">Star rating</option>
          </select>
          <button class="map-btn" @click="showMap">🗺️ Map</button>
        </div>
      </div>
    </div>

    <!-- Content Wrapper -->
    <div class="container">
      <div class="content-wrapper">
        <!-- Sidebar -->
        <div class="sidebar">
          <!-- Price Toggle -->
          <div class="filter-section">
            <div class="price-toggle">
              <span>Price Per Person</span>
              <div
                class="toggle-switch"
                :class="{ active: priceToggle }"
                @click="priceToggle = !priceToggle"
              ></div>
              <span>Total Price</span>
            </div>
          </div>

          <!-- Popular Filters -->
          <div class="filter-section">
            <div class="filter-title" @click="toggleFilterSection('popular')">
              Popular filters a
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
                  @click="
                    filters.fiveStar = !filters.fiveStar;
                    applyFilters();
                  "
                >
                  5-star hotel
                </div>
                <div
                  class="filter-tag outline"
                  :class="{ '': filters.fourPlusRating }"
                  @click="
                    filters.fourPlusRating = !filters.fourPlusRating;
                    applyFilters();
                  "
                >
                  4+ Tripadvisor rating
                </div>
                <div
                  class="filter-tag outline"
                  :class="{ '': filters.beach }"
                  @click="
                    filters.beach = !filters.beach;
                    applyFilters();
                  "
                >
                  Beach
                </div>
              </div>
            </div>
          </div>

          <!-- Promotions -->
          <div class="filter-section">
            <div
              class="filter-title"
              @click="toggleFilterSection('promotions')"
            >
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
                <div
                  class="filter-item"
                  @click="toggleFilterCheckbox('exclusive')"
                >
                  <div class="filter-item-left">
                    <div
                      class="filter-checkbox"
                      :class="{ checked: filters.exclusive }"
                    ></div>
                    <span class="filter-label">Exclusive</span>
                  </div>
                </div>
                <div
                  class="filter-item"
                  @click="toggleFilterCheckbox('freeChildStays')"
                >
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

          <!-- Popular Resorts -->
          <div class="filter-section">
            <div
              class="filter-title"
              @click="toggleFilterSection('popularResorts')"
            >
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

          <!-- Board Basis -->
          <div class="filter-section">
            <div
              class="filter-title"
              @click="toggleFilterSection('boardBasis')"
            >
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

          <!-- Star Rating -->
          <div class="filter-section">
            <div
              class="filter-title"
              @click="toggleFilterSection('starRating')"
            >
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

          <!-- Tripadvisor Score -->
          <div class="filter-section">
            <div
              class="filter-title"
              @click="toggleFilterSection('tripadvisor')"
            >
              Tripadvisor score
              <svg
                class="arrow"
                :class="{ collapsed: !expandedSections.tripadvisor }"
                viewBox="0 0 24 24"
              >
                <path d="M7 10l5 5 5-5z" />
              </svg>
            </div>
            <div
              class="filter-content"
              :class="{ collapsed: !expandedSections.tripadvisor }"
            >
              <div class="filter-list">
                <div
                  class="filter-item"
                  @click="toggleRadio('tripadvisor', 'any')"
                >
                  <div class="filter-item-left">
                    <div
                      class="filter-radio"
                      :class="{ checked: filters.tripadvisorScore === 'any' }"
                      data-group="tripadvisor"
                    ></div>
                    <span class="filter-label">Any</span>
                  </div>
                </div>
                <div
                  class="filter-item"
                  @click="toggleRadio('tripadvisor', '5')"
                >
                  <div class="filter-item-left">
                    <div
                      class="filter-radio"
                      :class="{ checked: filters.tripadvisorScore === '5' }"
                      data-group="tripadvisor"
                    ></div>
                    <span class="filter-label">5</span>
                  </div>
                  <div class="filter-count">1</div>
                </div>
                <div
                  class="filter-item"
                  @click="toggleRadio('tripadvisor', '4.5')"
                >
                  <div class="filter-item-left">
                    <div
                      class="filter-radio"
                      :class="{ checked: filters.tripadvisorScore === '4.5' }"
                      data-group="tripadvisor"
                    ></div>
                    <span class="filter-label">4.5 and above</span>
                  </div>
                  <div class="filter-count">12</div>
                </div>
                <div
                  class="filter-item"
                  @click="toggleRadio('tripadvisor', '4')"
                >
                  <div class="filter-item-left">
                    <div
                      class="filter-radio"
                      :class="{ checked: filters.tripadvisorScore === '4' }"
                      data-group="tripadvisor"
                    ></div>
                    <span class="filter-label">4 and above</span>
                  </div>
                  <div class="filter-count">12</div>
                </div>
                <div
                  class="filter-item"
                  @click="toggleRadio('tripadvisor', '3.5')"
                >
                  <div class="filter-item-left">
                    <div
                      class="filter-radio"
                      :class="{ checked: filters.tripadvisorScore === '3.5' }"
                      data-group="tripadvisor"
                    ></div>
                    <span class="filter-label">3.5 and above</span>
                  </div>
                  <div class="filter-count">12</div>
                </div>
                <div
                  class="filter-item"
                  @click="toggleRadio('tripadvisor', '3')"
                >
                  <div class="filter-item-left">
                    <div
                      class="filter-radio"
                      :class="{ checked: filters.tripadvisorScore === '3' }"
                      data-group="tripadvisor"
                    ></div>
                    <span class="filter-label">3 and above</span>
                  </div>
                  <div class="filter-count">12</div>
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
                    v-model="filters.maxPrice"
                    @input="updatePriceRange"
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

          <!-- Holiday Type -->
          <div class="filter-section">
            <div
              class="filter-title"
              @click="toggleFilterSection('holidayType')"
            >
              Holiday type
              <svg
                class="arrow"
                :class="{ collapsed: !expandedSections.holidayType }"
                viewBox="0 0 24 24"
              >
                <path d="M7 10l5 5 5-5z" />
              </svg>
            </div>
            <div
              class="filter-content"
              :class="{ collapsed: !expandedSections.holidayType }"
            >
              <div class="filter-list">
                <div
                  v-for="type in holidayTypes"
                  :key="type.name"
                  class="filter-item"
                  @click="toggleHolidayType(type.name)"
                >
                  <div class="filter-item-left">
                    <div
                      class="filter-checkbox"
                      :class="{
                        checked: filters.holidayTypes.includes(type.name),
                      }"
                    ></div>
                    <span class="filter-label">{{ type.name }}</span>
                  </div>
                  <div class="filter-count" v-if="type.count">
                    {{ type.count }}
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Outbound Times -->
          <div class="filter-section">
            <div
              class="filter-title"
              @click="toggleFilterSection('outboundTimes')"
            >
              Outbound times
              <svg
                class="arrow"
                :class="{ collapsed: !expandedSections.outboundTimes }"
                viewBox="0 0 24 24"
              >
                <path d="M7 10l5 5 5-5z" />
              </svg>
            </div>
            <div
              class="filter-content"
              :class="{ collapsed: !expandedSections.outboundTimes }"
            >
              <div class="filter-list">
                <div
                  class="filter-item"
                  @click="toggleFilterCheckbox('morningOut')"
                >
                  <div class="filter-item-left">
                    <div
                      class="filter-checkbox"
                      :class="{ checked: filters.morningOut }"
                    ></div>
                    <div class="time-slot">
                      <span class="filter-label">Morning</span>
                      <span class="time-range">06:00-11:59</span>
                    </div>
                  </div>
                </div>
                <div
                  class="filter-item"
                  @click="toggleFilterCheckbox('afternoonOut')"
                >
                  <div class="filter-item-left">
                    <div
                      class="filter-checkbox"
                      :class="{ checked: filters.afternoonOut }"
                    ></div>
                    <div class="time-slot">
                      <span class="filter-label">Afternoon</span>
                      <span class="time-range">12:00-17:59</span>
                    </div>
                  </div>
                </div>
                <div
                  class="filter-item"
                  @click="toggleFilterCheckbox('eveningOut')"
                >
                  <div class="filter-item-left">
                    <div
                      class="filter-checkbox"
                      :class="{ checked: filters.eveningOut }"
                    ></div>
                    <div class="time-slot">
                      <span class="filter-label">Evening</span>
                      <span class="time-range">18:00-23:59</span>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Inbound Times -->
          <div class="filter-section">
            <div
              class="filter-title"
              @click="toggleFilterSection('inboundTimes')"
            >
              Inbound times
              <svg
                class="arrow"
                :class="{ collapsed: !expandedSections.inboundTimes }"
                viewBox="0 0 24 24"
              >
                <path d="M7 10l5 5 5-5z" />
              </svg>
            </div>
            <div
              class="filter-content"
              :class="{ collapsed: !expandedSections.inboundTimes }"
            >
              <div class="filter-list">
                <div
                  class="filter-item"
                  @click="toggleFilterCheckbox('morningIn')"
                >
                  <div class="filter-item-left">
                    <div
                      class="filter-checkbox"
                      :class="{ checked: filters.morningIn }"
                    ></div>
                    <div class="time-slot">
                      <span class="filter-label">Morning</span>
                      <span class="time-range">00:00-11:59</span>
                    </div>
                  </div>
                </div>
                <div
                  class="filter-item"
                  @click="toggleFilterCheckbox('afternoonIn')"
                >
                  <div class="filter-item-left">
                    <div
                      class="filter-checkbox"
                      :class="{ checked: filters.afternoonIn }"
                    ></div>
                    <div class="time-slot">
                      <span class="filter-label">Afternoon</span>
                      <span class="time-range">12:00-17:59</span>
                    </div>
                  </div>
                </div>
                <div
                  class="filter-item"
                  @click="toggleFilterCheckbox('eveningIn')"
                >
                  <div class="filter-item-left">
                    <div
                      class="filter-checkbox"
                      :class="{ checked: filters.eveningIn }"
                    ></div>
                    <div class="time-slot">
                      <span class="filter-label">Evening</span>
                      <span class="time-range">18:00-23:59</span>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Facilities -->
          <div class="filter-section">
            <div
              class="filter-title"
              @click="toggleFilterSection('facilities')"
            >
              Facilities
              <svg
                class="arrow"
                :class="{ collapsed: !expandedSections.facilities }"
                viewBox="0 0 24 24"
              >
                <path d="M7 10l5 5 5-5z" />
              </svg>
            </div>
            <div
              class="filter-content"
              :class="{ collapsed: !expandedSections.facilities }"
            >
              <div class="filter-list">
                <div
                  v-for="facility in facilities"
                  :key="facility.name"
                  class="filter-item"
                  @click="toggleFacility(facility.name)"
                >
                  <div class="filter-item-left">
                    <div
                      class="filter-checkbox"
                      :class="{
                        checked: filters.facilities.includes(facility.name),
                      }"
                    ></div>
                    <span class="filter-label">{{ facility.name }}</span>
                  </div>
                  <div class="filter-count" v-if="facility.count">
                    {{ facility.count }}
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Pool -->
          <div class="filter-section">
            <div class="filter-title" @click="toggleFilterSection('pool')">
              Pool
              <svg
                class="arrow"
                :class="{ collapsed: !expandedSections.pool }"
                viewBox="0 0 24 24"
              >
                <path d="M7 10l5 5 5-5z" />
              </svg>
            </div>
            <div
              class="filter-content"
              :class="{ collapsed: !expandedSections.pool }"
            >
              <div class="filter-list">
                <div
                  v-for="pool in poolOptions"
                  :key="pool.name"
                  class="filter-item"
                  @click="togglePool(pool.name)"
                >
                  <div class="filter-item-left">
                    <div
                      class="filter-checkbox"
                      :class="{
                        checked: filters.pools.includes(pool.name),
                      }"
                    ></div>
                    <span class="filter-label">{{ pool.name }}</span>
                  </div>
                  <div class="filter-count" v-if="pool.count">
                    {{ pool.count }}
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Kids -->
          <div class="filter-section">
            <div class="filter-title" @click="toggleFilterSection('kids')">
              <div class="title-left">
                <span>Kids</span>
              </div>
              <svg
                class="arrow"
                :class="{ collapsed: !expandedSections.kids }"
                viewBox="0 0 24 24"
              >
                <path d="M7 10l5 5 5-5z" />
              </svg>
            </div>
            <div
              class="filter-content"
              :class="{ collapsed: !expandedSections.kids }"
            >
              <div class="filter-list">
                <div
                  v-for="kid in kidsOptions"
                  :key="kid.name"
                  class="filter-item"
                  @click="toggleKids(kid.name)"
                >
                  <div class="filter-item-left">
                    <div
                      class="filter-checkbox"
                      :class="{
                        checked: filters.kids.includes(kid.name),
                      }"
                    ></div>
                    <span class="filter-label">{{ kid.name }}</span>
                  </div>
                  <div class="filter-count" v-if="kid.count">
                    {{ kid.count }}
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Hotel Listings -->
        <div class="hotel-list">
          <div class="hotel-grid">
            <div
              v-for="hotel in sortedHotels"
              :key="hotel.id"
              class="hotel-card-new"
            >
              <div class="hotel-image-container">
                <img :src="hotel.image" :alt="hotel.name" />

                <!-- Free child stay badge -->
                <div class="free-child-badge">Free child stay</div>

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

              <div class="hotel-content-new">
                <h3 class="hotel-name-new">{{ hotel.name }}</h3>

                <div class="hotel-location-new">{{ hotel.location }}</div>

                <div class="hotel-separator"></div>

                <div class="hotel-bottom">
                  <div class="hotel-pricing-new">
                    <div class="price-label">From</div>
                    <div class="current-price-new">
                      {{ hotel.currentPrice }}
                      <span class="price-note-new">{{ hotel.priceNote }}</span>
                    </div>
                  </div>

                  <button class="discover-btn" @click="viewDeal(hotel)">
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
        </div>
      </div>
    </div>

    <!-- Recently Viewed Hotels -->
    <RecentlyViewed />
  </div>
</template>

<script>
export default {
  name: "SearchComponent",
  data() {
    return {
      searchQuery: "Dubai",
      departureAirport: "",
      departureDate: "Nov 2025",
      duration: "5 nights",
      rooms: "1 Room / 2 Adults",
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
        stars: [5],
        boardBasis: [],
        maxPrice: 5000,
        resorts: ["Jumeirah Beach"],
        holidayTypes: [],
        tripadvisorScore: "any",
        morningOut: false,
        afternoonOut: false,
        eveningOut: false,
        morningIn: false,
        afternoonIn: false,
        eveningIn: false,
        facilities: [],
        pools: [],
        kids: [],
      },

      props: [
        "searchQuery",
        "departureAirport",
        "departureDate",
        "duration",
        "rooms",
      ],
      methods: {
        filterHotels() {
          this.$emit("filter-hotels");
        },
      },

      // Expanded sections - All open by default
      expandedSections: {
        popular: true,
        promotions: true,
        starRating: true,
        boardBasis: true,
        maxPrice: true,
        popularResorts: true,
        holidayType: true,
        tripadvisor: true,
        outboundTimes: true,
        inboundTimes: true,
        facilities: true,
        pool: true,
        kids: true,
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

      holidayTypes: [
        { name: "Adults Only Hotels", count: null },
        { name: "Beach", count: 9 },
        { name: "Close to Airport", count: null },
        { name: "Couples", count: 10 },
        { name: "Family", count: 3 },
        { name: "Honeymoon", count: 2 },
        { name: "Luxury Hotels", count: 5 },
        { name: "Spa and Relaxation", count: 2 },
      ],

      facilities: [
        { name: "Air Conditioning", count: 13 },
        { name: "Bar", count: 8 },
        { name: "Beauty Treatments", count: 6 },
        { name: "Disabled Facilities", count: null },
        { name: "Gym", count: 5 },
        { name: "Massage", count: 6 },
        { name: "Satellite TV", count: 2 },
        { name: "WiFi", count: 8 },
      ],

      poolOptions: [
        { name: "Indoor Pool", count: null },
        { name: "Infinity Pool", count: null },
        { name: "Kids Pool", count: 3 },
        { name: "Outdoor Pool", count: null },
        { name: "Pool Bar", count: 4 },
      ],

      kidsOptions: [
        { name: "Kids Club", count: 14 },
        { name: "Kids Pool", count: 3 },
        { name: "Playground", count: 12 },
        { name: "Theme Parks", count: null },
        { name: "Water Slides", count: 5 },
      ],
    };
  },

  async mounted() {
    try {
      const response = await fetch("/data/hotels.json");
      const data = await response.json();
      this.hotels = data.hotels;
      this.applyFilters();
    } catch (error) {
      console.error("Failed to load hotels:", error);
      this.hotels = [];
      this.filteredHotels = [];
    }
  },

  computed: {
    resultsTitle() {
      return `${this.filteredHotels.length} holidays found`;
    },

    sortedHotels() {
      const hotels = [...this.filteredHotels];

      switch (this.sortBy) {
        case "price-low":
          return hotels.sort((a, b) => {
            const priceA = parseInt(a.currentPrice.replace(/[£,]/g, ""));
            const priceB = parseInt(b.currentPrice.replace(/[£,]/g, ""));
            return priceA - priceB;
          });
        case "price-high":
          return hotels.sort((a, b) => {
            const priceA = parseInt(a.currentPrice.replace(/[£,]/g, ""));
            const priceB = parseInt(b.currentPrice.replace(/[£,]/g, ""));
            return priceB - priceA;
          });
        case "rating":
          return hotels.sort((a, b) => b.stars - a.stars);
        default:
          return hotels;
      }
    },

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
    filterHotels() {
      this.applyFilters();
    },

    applyFilters() {
      let filtered = [...this.hotels];

      // Search query filter
      if (this.searchQuery.trim()) {
        const query = this.searchQuery.toLowerCase();
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

      // Board basis filter
      if (this.filters.boardBasis.length > 0) {
        filtered = filtered.filter((hotel) =>
          this.filters.boardBasis.includes(hotel.boardBasis)
        );
      }

      // Price filter
      if (this.filters.maxPrice < 5000) {
        filtered = filtered.filter((hotel) => {
          const price = parseInt(hotel.currentPrice.replace(/[£,]/g, ""));
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

      // Beach filter
      if (this.filters.beach) {
        filtered = filtered.filter(
          (hotel) =>
            hotel.category.toLowerCase().includes("beach") ||
            hotel.location.toLowerCase().includes("beach")
        );
      }

      // Four plus rating filter
      if (this.filters.fourPlusRating) {
        filtered = filtered.filter((hotel) => hotel.rating >= 4.0);
      }

      // Tripadvisor score filter
      if (this.filters.tripadvisorScore !== "any") {
        const minScore = parseFloat(this.filters.tripadvisorScore);
        filtered = filtered.filter((hotel) => hotel.rating >= minScore);
      }

      this.filteredHotels = filtered;
    },

    sortHotels() {
      // Reactivity is handled by computed property
    },

    showMap() {
      alert("Map view would be implemented here");
    },

    viewDeal(hotel) {
      // Navigate to hotel detail page
      this.$router.push(`/hotel-detail?hotelId=${hotel.id}`);
    },

    toggleFilterSection(section) {
      this.expandedSections[section] = !this.expandedSections[section];
    },

    toggleFilterCheckbox(filterName) {
      this.filters[filterName] = !this.filters[filterName];
      this.applyFilters();
    },

    toggleStarFilter(stars) {
      const index = this.filters.stars.indexOf(stars);
      if (index > -1) {
        this.filters.stars.splice(index, 1);
      } else {
        this.filters.stars.push(stars);
      }
      this.applyFilters();
    },

    toggleBoardBasis(basis) {
      const index = this.filters.boardBasis.indexOf(basis);
      if (index > -1) {
        this.filters.boardBasis.splice(index, 1);
      } else {
        this.filters.boardBasis.push(basis);
      }
      this.applyFilters();
    },

    toggleResort(resort) {
      const index = this.filters.resorts.indexOf(resort);
      if (index > -1) {
        this.filters.resorts.splice(index, 1);
      } else {
        this.filters.resorts.push(resort);
      }
      this.applyFilters();
    },

    toggleHolidayType(type) {
      const index = this.filters.holidayTypes.indexOf(type);
      if (index > -1) {
        this.filters.holidayTypes.splice(index, 1);
      } else {
        this.filters.holidayTypes.push(type);
      }
      this.applyFilters();
    },

    toggleFacility(facility) {
      const index = this.filters.facilities.indexOf(facility);
      if (index > -1) {
        this.filters.facilities.splice(index, 1);
      } else {
        this.filters.facilities.push(facility);
      }
      this.applyFilters();
    },

    togglePool(pool) {
      const index = this.filters.pools.indexOf(pool);
      if (index > -1) {
        this.filters.pools.splice(index, 1);
      } else {
        this.filters.pools.push(pool);
      }
      this.applyFilters();
    },

    toggleKids(kid) {
      const index = this.filters.kids.indexOf(kid);
      if (index > -1) {
        this.filters.kids.splice(index, 1);
      } else {
        this.filters.kids.push(kid);
      }
      this.applyFilters();
    },

    toggleRadio(group, value) {
      if (group === "tripadvisor") {
        this.filters.tripadvisorScore = value;
        this.applyFilters();
      }
    },

    updatePriceRange() {
      this.applyFilters();
    },

    getStarCount(stars) {
      return this.hotels.filter((hotel) => hotel.stars === stars).length;
    },
  },
};
</script>

<style scoped>
/* Component-specific overrides only */
.search-area {
  padding: 0;
}

.filter-title.collapsed .arrow {
  transform: rotate(-90deg);
}

.filter-content.collapsed {
  max-height: 0;
  overflow: hidden;
}

.filter-content {
  max-height: 1000px;
  transition: max-height 0.3s ease-out;
}

.arrow {
  transition: transform 0.3s ease;
}

.time-slot {
  display: flex;
  flex-direction: column;
}

.time-range {
  font-size: 11px;
  color: #999;
}
</style>
