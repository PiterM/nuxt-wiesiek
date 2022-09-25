<script setup lang="ts">
import { ref, onMounted, onUnmounted, reactive } from "vue";

import NavigationItemsOffsets from "../../types/NavigationItemsOffsets";

const headerRef = ref(null);
const stickyClassEnabled = ref(false);
const navigationItems = ref<Array<HTMLElement>>([]);
const navigationItemsOffsets = reactive<NavigationItemsOffsets>({});
const currentNavigationHash = ref("");
const scrollingToElement = ref(false);

let targetWindowScrollY = 0;
let targetLocationHash = "";
let windowScrollY = 0;

const onWindowScroll = () => {
  windowScrollY = window.scrollY;
  if (windowScrollY >= 20 && !stickyClassEnabled.value) {
    stickyClassEnabled.value = true;
  } else if (windowScrollY < 20 && stickyClassEnabled.value) {
    stickyClassEnabled.value = false;
  }
  if (windowScrollY === targetWindowScrollY && scrollingToElement.value) {
    document.location.hash = targetLocationHash;
    scrollingToElement.value = false;
  }

  changeCurrentNavigationItemOnScroll();
};

const changeCurrentNavigationItemOnScroll = () => {
  Object.keys(navigationItemsOffsets).forEach(
    (offset: string, index: number) => {
      const nextOffset = Object.keys(navigationItemsOffsets)[index + 1];
      if (
        windowScrollY >= parseInt(offset) - 100 &&
        (windowScrollY < parseInt(nextOffset) || nextOffset === undefined) &&
        currentNavigationHash.value !== navigationItemsOffsets[offset]
      ) {
        changeCurrentNavigationItem(navigationItemsOffsets[offset]);
      }
    }
  );
};

const changeCurrentNavigationItem = (newCurrentElementHash: string) => {
  Object.values(navigationItems.value).forEach((item: HTMLElement) => {
    const parentLiElement = item.parentElement;
    if (
      item.getAttribute("href") === newCurrentElementHash &&
      parentLiElement!.className !== "current"
    ) {
      parentLiElement!.className = "current";
      currentNavigationHash.value = newCurrentElementHash;
    } else {
      parentLiElement!.className = "";
    }
  });
};

const scrollToElement = (event: Event) => {
  const hash = (event.target as HTMLElement).getAttribute("href");
  const targetElement = hash && document.getElementById(hash.replace("#", ""));
  if (targetElement) {
    targetWindowScrollY = targetElement.offsetTop;
    targetLocationHash = hash;
    scrollingToElement.value = true;
    window.scrollTo({
      left: 0,
      top: targetElement.offsetTop,
      behavior: "smooth",
    });
    changeCurrentNavigationItem(hash);
  }
};

onMounted(() => {
  window.addEventListener("scroll", onWindowScroll);

  navigationItems.value = document.getElementsByClassName(
    "navigation-item"
  ) as any;
  Object.values(navigationItems.value).forEach((item: HTMLElement) => {
    const href = (item as HTMLElement).getAttribute("href")!;
    const key = String(
      document.getElementById(href?.replace("#", "")!)?.offsetTop
    );
    if (key && href) {
      navigationItemsOffsets[key] = href;
    }
  });
});

onUnmounted(() => {
  window.removeEventListener("scroll", onWindowScroll);
});
</script>

<template>
  <!-- site header
    ================================================== -->
  <header
    class="s-header"
    :class="{ sticky: stickyClassEnabled }"
    ref="headerRef"
  >
    <div class="header-logo">
      <a class="site-logo" href="/">
        <img src="images/logo.png" alt="Homepage" />
      </a>
    </div>

    <nav class="header-nav-wrap">
      <ul class="header-main-nav">
        <li class="current">
          <a
            @click.prevent.stop="scrollToElement"
            href="#intro"
            title="intro"
            class="navigation-item"
            >Intro</a
          >
        </li>
        <li>
          <a
            @click.prevent.stop="scrollToElement"
            href="#about"
            title="o mnie"
            class="navigation-item"
            >O mnie</a
          >
        </li>
        <li>
          <a
            @click.prevent.stop="scrollToElement"
            href="#music"
            title="muzyka"
            class="navigation-item"
            >Muzyka</a
          >
        </li>
        <li>
          <a
            @click.prevent.stop="scrollToElement"
            title="wiersze"
            class="navigation-item"
            >Wiersze</a
          >
        </li>
        <li>
          <a
            @click.prevent.stop="scrollToElement"
            title="obrazy"
            class="navigation-item"
            >Obrazy</a
          >
        </li>
        <li>
          <a
            @click.prevent.stop="scrollToElement"
            title="książki"
            class="navigation-item"
            >Książki</a
          >
        </li>
        <li>
          <a
            @click.prevent.stop="scrollToElement"
            title="kontakt"
            class="navigation-item"
            >Kontakt</a
          >
        </li>
      </ul>

      <ul class="header-social">
        <li>
          <a href="#0"><i class="fab fa-facebook-f" aria-hidden="true"></i></a>
        </li>
        <li>
          <a href="#0"><i class="fab fa-youtube" aria-hidden="true"></i></a>
        </li>
      </ul>
    </nav>

    <a class="header-menu-toggle" href="#"><span>Menu</span></a>
  </header>
  <!-- end s-header -->
</template>

<style scoped lang="sass">
.navigation-item
  cursor: pointer
</style>
