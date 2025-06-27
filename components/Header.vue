<template>
  <div>
    <!-- Desktop Header -->
    <HeaderDesktop
      v-if="!isMobile"
      :menuData="menuData"
      @open-enquiry-modal="$emit('open-enquiry-modal')"
    />

    <!-- Mobile Header -->
    <HeaderMobile
      v-else
      :menuData="menuData"
      @open-enquiry-modal="$emit('open-enquiry-modal')"
    />
  </div>
</template>

<script>
import HeaderDesktop from "@/components/HeaderDesktop.vue";
import HeaderMobile from "@/components/HeaderMobile.vue";
import menuData from "~/static/data/menu.json";

export default {
  name: "AppHeader",
  components: {
    HeaderDesktop,
    HeaderMobile,
  },
  emits: ["open-enquiry-modal"],
  data() {
    return {
      menuData,
      isMobile: false,
    };
  },
  methods: {
    checkDevice() {
      // Check if window is available (client-side)
      if (typeof window !== "undefined") {
        this.isMobile = window.innerWidth <= 768;
      }
    },
    handleResize() {
      this.checkDevice();
    },
  },
  mounted() {
    // Initial device check
    this.checkDevice();

    // Add resize listener
    if (typeof window !== "undefined") {
      window.addEventListener("resize", this.handleResize);
    }
  },
  beforeDestroy() {
    // Cleanup resize listener
    if (typeof window !== "undefined") {
      window.removeEventListener("resize", this.handleResize);
    }
  },
};
</script>

<style scoped>
/* Component-specific styles if needed */
</style>
