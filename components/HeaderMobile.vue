<template>
  <header>
    <nav class="mobile-header" data-v-7d7db6f0="">
      <div class="mobile-header-container" data-v-7d7db6f0="">
        <!-- Phone Number -->
        <a
          :href="`tel:${menuData.contact.phone}`"
          class="call-times InfinityNumber"
          data-v-7d7db6f0=""
        >
          <svg
            data-v-7d7db6f0=""
            aria-hidden="true"
            focusable="false"
            data-prefix="fas"
            data-icon="phone"
            role="img"
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 512 512"
            class="svg-inline--fa fa-phone"
          >
            <path
              data-v-7d7db6f0=""
              fill="currentColor"
              d="M164.9 24.6c-7.7-18.6-28-28.5-47.4-23.2l-88 24C12.1 30.2 0 46 0 64C0 311.4 200.6 512 448 512c18 0 33.8-12.1 38.6-29.5l24-88c5.3-19.4-4.6-39.7-23.2-47.4l-96-40c-16.3-6.8-35.2-2.1-46.3 11.6L304.7 368C234.3 334.7 177.3 277.7 144 207.3L193.3 167c13.7-11.2 18.4-30 11.6-46.3l-40-96z"
              class=""
            ></path>
          </svg>
          <span data-v-7d7db6f0="">
            {{ menuData.contact.hours }}
          </span>
        </a>

        <!-- Logo -->
        <nuxt-link
          :to="menuData.logo.url"
          aria-current="page"
          itemprop="url"
          class="logo nuxt-link-exact-active nuxt-link-active"
          data-v-7d7db6f0=""
        >
          <img
            :src="menuData.logo.src"
            :alt="menuData.logo.alt"
            data-v-7d7db6f0=""
          />
        </nuxt-link>

        <!-- Action Buttons -->
        <div class="mobile-action-buttons" data-v-7d7db6f0="">
          <div class="search-icon" data-v-7d7db6f0="" @click="toggleSearch">
            <svg
              aria-hidden="true"
              focusable="false"
              data-prefix="fas"
              data-icon="magnifying-glass"
              role="img"
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 512 512"
              class="svg-inline--fa fa-magnifying-glass"
              data-v-7d7db6f0=""
            >
              <path
                fill="currentColor"
                d="M416 208c0 45.9-14.9 88.3-40 122.7L502.6 457.4c12.5 12.5 12.5 32.8 0 45.3s-32.8 12.5-45.3 0L330.7 376c-34.4 25.2-76.8 40-122.7 40C93.1 416 0 322.9 0 208S93.1 0 208 0S416 93.1 416 208zM208 352a144 144 0 1 0 0-288 144 144 0 1 0 0 288z"
                data-v-7d7db6f0=""
                class=""
              ></path>
            </svg>
          </div>
          <div class="menu-icon" data-v-7d7db6f0="" @click="toggleMenu">
            <svg
              aria-hidden="true"
              focusable="false"
              data-prefix="fas"
              data-icon="bars"
              role="img"
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 448 512"
              class="svg-inline--fa fa-bars"
              data-v-7d7db6f0=""
            >
              <path
                fill="currentColor"
                d="M0 96C0 78.3 14.3 64 32 64l384 0c17.7 0 32 14.3 32 32s-14.3 32-32 32L32 128C14.3 128 0 113.7 0 96zM0 256c0-17.7 14.3-32 32-32l384 0c17.7 0 32 14.3 32 32s-14.3 32-32 32L32 288c-17.7 0-32-14.3-32-32zM448 416c0 17.7-14.3 32-32 32L32 448c-17.7 0-32-14.3-32-32s14.3-32 32-32l384 0c17.7 0 32 14.3 32 32z"
                data-v-7d7db6f0=""
                class=""
              ></path>
            </svg>
          </div>
        </div>
      </div>

      <!-- Mobile Navigation Menu -->
      <div
        id="mobileNavContainer"
        class="mobile-nav-container"
        :class="{ 'nav-open': isMenuOpen }"
        data-v-7d7db6f0=""
      >
        <div class="mobile-nav-menu" data-v-7d7db6f0="">
          <!-- Destinations Dropdown -->
          <div
            v-for="(item, idx) in menuData.menu"
            :key="idx"
            class="mobile-menu-dropdown-item"
            data-v-7d7db6f0=""
            v-if="item.type === 'dropdown'"
          >
            <div
              :data-dropdown="getDataDropdownValue(item.label)"
              data-v-7d7db6f0=""
              @click="toggleDropdown(getDataDropdownValue(item.label))"
            >
              <span data-v-7d7db6f0="">{{ item.label }}</span>
              <svg
                aria-hidden="true"
                focusable="false"
                data-prefix="fas"
                data-icon="chevron-down"
                role="img"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 512 512"
                class="mobile-menu-arrow svg-inline--fa fa-chevron-down"
                :class="{
                  rotated: expandedDropdowns[getDataDropdownValue(item.label)],
                }"
                data-v-7d7db6f0=""
              >
                <path
                  fill="currentColor"
                  d="M233.4 406.6c12.5 12.5 32.8 12.5 45.3 0l192-192c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0L256 338.7 86.6 169.4c-12.5-12.5-32.8-12.5-45.3 0s-12.5 32.8 0 45.3l192 192z"
                  data-v-7d7db6f0=""
                  class=""
                ></path>
              </svg>
            </div>

            <!-- Dropdown Content -->
            <div
              :data-dropdown-type="getDataDropdownValue(item.label)"
              class="mobile-menu-dropdown-content"
              :class="{
                expanded: expandedDropdowns[getDataDropdownValue(item.label)],
              }"
              data-v-7d7db6f0=""
            >
              <!-- View All Link (for Destinations) -->
              <a
                v-if="item.footer"
                :href="item.footer.url"
                data-v-7d7db6f0=""
                class=""
              >
                {{ item.footer.label }}
              </a>

              <!-- Multi-column dropdown (like Destinations) -->
              <template v-if="item.columns">
                <div
                  v-for="(col, cIdx) in item.columns"
                  :key="cIdx"
                  data-v-7d7db6f0=""
                  class="mobile-menu-nested-dropdown-item"
                >
                  <p
                    data-v-7d7db6f0=""
                    :data-dropdown="`${getDataDropdownValue(
                      item.label
                    )}-${cIdx}`"
                    @click="
                      toggleNestedDropdown(
                        `${getDataDropdownValue(item.label)}-${cIdx}`
                      )
                    "
                  >
                    {{ col.title }}
                    <svg
                      data-v-7d7db6f0=""
                      aria-hidden="true"
                      focusable="false"
                      data-prefix="fas"
                      data-icon="chevron-down"
                      role="img"
                      xmlns="http://www.w3.org/2000/svg"
                      viewBox="0 0 512 512"
                      class="mobile-menu-arrow svg-inline--fa fa-chevron-down"
                      :class="{
                        rotated:
                          expandedNestedDropdowns[
                            `${getDataDropdownValue(item.label)}-${cIdx}`
                          ],
                      }"
                    >
                      <path
                        data-v-7d7db6f0=""
                        fill="currentColor"
                        d="M233.4 406.6c12.5 12.5 32.8 12.5 45.3 0l192-192c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0L256 338.7 86.6 169.4c-12.5-12.5-32.8-12.5-45.3 0s-12.5 32.8 0 45.3l192 192z"
                        class=""
                      ></path>
                    </svg>
                  </p>
                  <div
                    data-v-7d7db6f0=""
                    :data-dropdown-type="`${getDataDropdownValue(
                      item.label
                    )}-${cIdx}`"
                    class="mobile-menu-dropdown-content"
                    :class="{
                      expanded:
                        expandedNestedDropdowns[
                          `${getDataDropdownValue(item.label)}-${cIdx}`
                        ],
                    }"
                  >
                    <div
                      v-for="(link, lIdx) in col.links"
                      :key="lIdx"
                      data-v-7d7db6f0=""
                    >
                      <a data-v-7d7db6f0="" :href="link.url" class="">
                        {{ link.label }}
                      </a>
                    </div>
                    <a
                      data-v-7d7db6f0=""
                      :href="`/destinations/${col.title
                        .toLowerCase()
                        .replace(/\s+/g, '-')}/hotels`"
                      class=""
                    >
                      View {{ col.title }}
                    </a>
                  </div>
                </div>
              </template>

              <!-- Simple dropdown (like Holiday Types) -->
              <template v-else>
                <a
                  v-for="(link, lIdx) in item.links"
                  :key="lIdx"
                  :href="link.url"
                  data-v-7d7db6f0=""
                  class=""
                >
                  {{ link.label }}
                </a>
              </template>
            </div>
          </div>

          <!-- Regular Link Items -->
          <a
            v-for="(item, idx) in menuData.menu"
            :key="`link-${idx}`"
            v-if="item.type === 'link'"
            :href="item.url"
            class="mobile-menu-flat-item"
            data-v-7d7db6f0=""
          >
            {{ item.label }}
          </a>
        </div>

        <!-- Mobile Nav Footer -->
        <div class="mobile-nav-footer" data-v-7d7db6f0="">
          <a
            :href="`tel:${menuData.contact.phone}`"
            class="InfinityNumber large-call-times"
            data-v-7d7db6f0=""
          >
            <div class="phone-pair" data-v-7d7db6f0="">
              <svg
                aria-hidden="true"
                focusable="false"
                data-prefix="fas"
                data-icon="phone"
                role="img"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 512 512"
                class="svg-inline--fa fa-phone"
                data-v-7d7db6f0=""
              >
                <path
                  fill="currentColor"
                  d="M164.9 24.6c-7.7-18.6-28-28.5-47.4-23.2l-88 24C12.1 30.2 0 46 0 64C0 311.4 200.6 512 448 512c18 0 33.8-12.1 38.6-29.5l24-88c5.3-19.4-4.6-39.7-23.2-47.4l-96-40c-16.3-6.8-35.2-2.1-46.3 11.6L304.7 368C234.3 334.7 177.3 277.7 144 207.3L193.3 167c13.7-11.2 18.4-30 11.6-46.3l-40-96z"
                  data-v-7d7db6f0=""
                  class=""
                ></path>
              </svg>
              <span class="InfinityNumber" data-v-7d7db6f0="">{{
                formatPhoneNumber(menuData.contact.phone)
              }}</span>
            </div>
            <div class="time-pair" data-v-7d7db6f0="">
              <svg
                aria-hidden="true"
                focusable="false"
                data-prefix="fas"
                data-icon="clock"
                role="img"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 512 512"
                class="svg-inline--fa fa-clock"
                data-v-7d7db6f0=""
              >
                <path
                  fill="currentColor"
                  d="M256 0a256 256 0 1 1 0 512A256 256 0 1 1 256 0zM232 120l0 136c0 8 4 15.5 10.7 20l96 64c11 7.4 25.9 4.4 33.3-6.7s4.4-25.9-6.7-33.3L280 243.2 280 120c0-13.3-10.7-24-24-24s-24 10.7-24 24z"
                  data-v-7d7db6f0=""
                  class=""
                ></path>
              </svg>
              <span data-v-7d7db6f0="">{{ menuData.contact.hours }}</span>
            </div>
          </a>

          <button
            id="pydMobileCTA"
            class="mobile-pyd-cta"
            data-v-7d7db6f0=""
            @click="$emit('open-enquiry-modal')"
          >
            <span class="mobile-pyd-text" data-v-7d7db6f0="">{{
              menuData.button.text
            }}</span>
            <svg
              aria-hidden="true"
              focusable="false"
              data-prefix="fas"
              data-icon="arrow-right"
              role="img"
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 448 512"
              class="svg-inline--fa fa-arrow-right"
              data-v-7d7db6f0=""
            >
              <path
                fill="currentColor"
                d="M438.6 278.6c12.5-12.5 12.5-32.8 0-45.3l-160-160c-12.5-12.5-32.8-12.5-45.3 0s-12.5 32.8 0 45.3L338.8 224 32 224c-17.7 0-32 14.3-32 32s14.3 32 32 32l306.7 0L233.4 393.4c-12.5 12.5-12.5 32.8 0 45.3s32.8 12.5 45.3 0l160-160z"
                data-v-7d7db6f0=""
                class=""
              ></path>
            </svg>
          </button>
        </div>
      </div>
    </nav>
  </header>
</template>

<script>
export default {
  name: "HeaderMobile",
  props: {
    menuData: {
      type: Object,
      required: true,
    },
  },
  emits: ["open-enquiry-modal"],
  data() {
    return {
      isMenuOpen: false,
      expandedDropdowns: {},
      expandedNestedDropdowns: {},
    };
  },
  methods: {
    formatPhoneNumber(phone) {
      // Format phone number like "01204 269010"
      return phone.replace(/(\d{5})(\d{6})/, "$1&nbsp;$2");
    },
    getDataDropdownValue(label) {
      return label.toLowerCase().replace(/\s+/g, "-");
    },
    toggleMenu() {
      this.isMenuOpen = !this.isMenuOpen;

      // Prevent body scroll when menu is open
      if (this.isMenuOpen) {
        document.body.style.overflow = "hidden";
      } else {
        document.body.style.overflow = "";
      }
    },
    toggleSearch() {
      // Handle search functionality
      console.log("Search clicked");
    },
    toggleDropdown(dropdownKey) {
      this.expandedDropdowns[dropdownKey] =
        !this.expandedDropdowns[dropdownKey];

      // Close all other dropdowns
      Object.keys(this.expandedDropdowns).forEach((key) => {
        if (key !== dropdownKey) {
          this.expandedDropdowns[key] = false;
        }
      });

      // Force reactivity
      this.$forceUpdate();
    },
    toggleNestedDropdown(nestedKey) {
      this.expandedNestedDropdowns[nestedKey] =
        !this.expandedNestedDropdowns[nestedKey];

      // Close all other nested dropdowns
      Object.keys(this.expandedNestedDropdowns).forEach((key) => {
        if (key !== nestedKey) {
          this.expandedNestedDropdowns[key] = false;
        }
      });

      // Force reactivity
      this.$forceUpdate();
    },
    closeMenu() {
      this.isMenuOpen = false;
      document.body.style.overflow = "";

      // Reset all dropdowns
      this.expandedDropdowns = {};
      this.expandedNestedDropdowns = {};
    },
  },
  mounted() {
    // Close menu when clicking outside
    document.addEventListener("click", (e) => {
      const mobileHeader = this.$el;
      if (!mobileHeader.contains(e.target) && this.isMenuOpen) {
        this.closeMenu();
      }
    });

    // Close menu on route change (if using router)
    if (this.$router) {
      this.$router.afterEach(() => {
        this.closeMenu();
      });
    }
  },
  beforeDestroy() {
    // Cleanup
    document.body.style.overflow = "";
  },
};
</script>

<style scoped>
/* Mobile Header Styles - Based on Destinology CSS */
.mobile-header {
  background-color: var(--destinology-almond, #e6d7cb);
  height: 80px;
  top: 0;
  transition: all 0.2s linear;
  width: 100%;
  z-index: 9999999;
  position: fixed;
}

.mobile-header-container {
  align-items: center;
  display: flex;
  gap: 8px;
  height: 100%;
  justify-content: space-between;
  padding: 0.4em 1.2em;
  width: 100%;
}

.mobile-header.fixed-nav-bar {
  height: 64px;
  position: fixed;
}

.call-times {
  align-items: center;
  color: var(--destinology-black, #1f1f1f);
  display: flex;
  font-size: 10px;
  gap: 6px;
  justify-content: flex-start;
  text-decoration: none;
}

.call-times,
.mobile-action-buttons {
  align-items: center;
  display: flex;
  flex-direction: row;
  justify-content: center;
  width: 92px;
}

.logo {
  height: 25px;
}

.logo > img {
  height: 100%;
  width: 100%;
}

.mobile-action-buttons {
  gap: 20px;
  justify-content: flex-end !important;
}

.call-times svg,
.search-icon svg,
.search-icon {
  cursor: pointer;
  display: inline-block;
  height: 20px;
  width: 20px;
}

.menu-icon svg,
.menu-icon {
  cursor: pointer;
  display: inline-block;
  height: 24px;
  width: 20px;
}

/* Mobile Navigation */
.mobile-nav-container {
  background-color: var(--destinology-almond, #e6d7cb);
  color: var(--destinology-black, #1f1f1f);
  display: block;
  height: 100%;
  position: fixed;
  right: -100vw;
  transition: all 0.2s;
  width: 100vw;
  z-index: 99998;
  top: 80px;
}

.mobile-nav-container.nav-open {
  height: 100%;
  right: 0;
}

.mobile-nav-menu {
  height: calc(100% - 170px);
  overflow-y: auto;
  width: 100%;
}

.mobile-menu-flat-item {
  border-bottom: 1px solid var(--destinology-black, #1f1f1f);
  color: var(--destinology-black, #1f1f1f);
  cursor: pointer;
  display: block;
  font-size: 16px;
  height: 54px;
  line-height: 24px;
  margin: 0;
  padding: 15px 25px;
  white-space: nowrap;
  width: 100%;
  text-decoration: none;
}

.mobile-menu-dropdown-item {
  display: flex;
  flex-direction: column;
  min-height: 54px;
}

.mobile-menu-dropdown-item > div:first-child,
.mobile-menu-nested-dropdown-item > p {
  align-items: center;
  border-bottom: 1px solid var(--destinology-black, #1f1f1f);
  color: var(--destinology-black, #1f1f1f);
  cursor: pointer;
  display: flex;
  flex-direction: row;
  font-size: 16px;
  justify-content: space-between;
  line-height: 24px;
  margin: 0;
  padding: 15px 25px;
  white-space: nowrap;
}

.mobile-menu-dropdown-item > div:first-child > span {
  pointer-events: none;
}

.mobile-menu-arrow {
  pointer-events: none;
  transform: rotate(0deg);
  transition: all 0.5s;
}

.mobile-menu-arrow.rotated {
  transform: rotate(-180deg);
  transition: all 0.5s;
}

.mobile-menu-dropdown-content {
  background-color: transparent;
  display: flex;
  flex-direction: column;
  max-height: 0;
  overflow: hidden;
  transition: all 0.5s;
}

.mobile-menu-dropdown-content.expanded {
  max-height: 1500px;
  overflow: hidden;
}

.mobile-menu-dropdown-content > .mobile-menu-nested-dropdown-item > a,
.mobile-menu-dropdown-content > a {
  border-bottom: 1px solid var(--destinology-black, #1f1f1f);
  color: var(--destinology-black, #1f1f1f);
  font-size: 14px;
  line-height: 22px;
  padding: 15px 0 15px 40px;
  text-decoration: none;
}

.mobile-menu-nested-dropdown-item.indent1 {
  padding-left: 40px;
}

.mobile-menu-nested-dropdown-item.indent2 {
  padding-left: 60px;
}

.mobile-menu-nested-dropdown-item > a,
.mobile-menu-nested-dropdown-item > p {
  font-size: 14px;
  line-height: 22px;
  padding-left: 40px;
}

.mobile-menu-nested-dropdown-item .mobile-menu-dropdown-content > div {
  border-bottom: 1px solid var(--destinology-black, #1f1f1f);
  padding: 15px 0 15px 55px;
}

.mobile-menu-nested-dropdown-item .mobile-menu-dropdown-content > div > a {
  color: var(--destinology-black, #1f1f1f);
  font-size: 14px;
  line-height: 22px;
  text-decoration: none;
}

.mobile-menu-nested-dropdown-item .mobile-menu-dropdown-content > a {
  padding-left: 55px;
}

/* Mobile Nav Footer */
.mobile-nav-footer {
  bottom: 0;
  display: flex;
  flex-direction: column;
  height: 96px;
  position: fixed;
  width: 100vw;
}

.large-call-times {
  align-items: center;
  background-color: var(--destinology-almond, #e6d7cb);
  color: var(--destinology-black, #1f1f1f);
  display: flex;
  font-size: 14px;
  height: 48px;
  justify-content: space-evenly;
  line-height: 22px;
  text-decoration: none;
}

.phone-pair,
.time-pair {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 8px;
}

.phone-pair svg,
.time-pair svg {
  width: 16px;
  height: 16px;
}

.mobile-pyd-cta {
  align-items: center;
  background-color: var(--destinology-marine, #153b3c);
  border: 0;
  color: #fff;
  display: flex;
  gap: 16px;
  height: 48px;
  justify-content: center;
  width: 100%;
  cursor: pointer;
  transition: background-color 0.2s;
}

.mobile-pyd-cta:hover {
  background-color: var(--destinology-marine-faded, #20595b);
}

.mobile-pyd-cta svg {
  width: 16px;
  height: 16px;
}

/* CSS Variables for Destinology Colors */
:root {
  --destinology-black: #1f1f1f;
  --destinology-marine: #153b3c;
  --destinology-marine-faded: #20595b;
  --destinology-almond: #e6d7cb;
}

/* Responsive adjustments */
@media (max-width: 480px) {
  .mobile-header-container {
    padding: 8px 12px;
  }

  .call-times {
    font-size: 9px;
  }

  .logo {
    height: 22px;
  }

  .mobile-action-buttons {
    gap: 15px;
  }

  .call-times svg,
  .search-icon,
  .search-icon svg {
    width: 18px;
    height: 18px;
  }

  .menu-icon,
  .menu-icon svg {
    width: 18px;
    height: 22px;
  }
}
</style>
