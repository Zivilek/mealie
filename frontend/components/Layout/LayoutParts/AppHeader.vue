<template>
  <v-app-bar clipped-left dense app color="primary" dark class="d-print-none">
    <slot />
    <router-link :to="routerLink">
      <v-btn icon>
        <v-icon size="40"> {{ $globals.icons.primary }} </v-icon>
      </v-btn>
    </router-link>

    <div btn class="pl-2">
      <v-toolbar-title style="cursor: pointer" @click="$router.push(routerLink)"> Mealie </v-toolbar-title>
    </div>
    <RecipeDialogSearch ref="domSearchDialog" />

    <v-spacer></v-spacer>

    <!-- Navigation Menu -->
    <template v-if="menu">
      <div v-if="!$vuetify.breakpoint.xs" style="max-width: 500px" @click="activateSearch">
        <v-text-field
          readonly
          class="mt-6 rounded-xl"
          rounded
          dark
          solo
          dense
          flat
          :prepend-inner-icon="$globals.icons.search"
          background-color="primary darken-1"
          color="white"
          :placeholder="$t('search.search-hint')"
        >
        </v-text-field>
      </div>
      <v-btn v-else icon @click="activateSearch">
        <v-icon> {{ $globals.icons.search }}</v-icon>
      </v-btn>
      <v-btn v-if="$auth.loggedIn" :text="$vuetify.breakpoint.smAndUp" :icon="$vuetify.breakpoint.xs" @click="$auth.logout()">
        <v-icon :left="$vuetify.breakpoint.smAndUp">{{ $globals.icons.logout }}</v-icon>
        {{ $vuetify.breakpoint.smAndUp ? $t("user.logout") : "" }}
      </v-btn>
      <v-btn v-else text nuxt to="/login">
        <v-icon left>{{ $globals.icons.user }}</v-icon>
        {{ $t("user.login") }}
      </v-btn>
    </template>
  </v-app-bar>
</template>

<script lang="ts">
import { computed, defineComponent, onBeforeUnmount, onMounted, ref, useContext, useRoute } from "@nuxtjs/composition-api";
import RecipeDialogSearch from "~/components/Domain/Recipe/RecipeDialogSearch.vue";

export default defineComponent({
  components: { RecipeDialogSearch },
  props: {
    menu: {
      type: Boolean,
      default: true,
    },
  },
  setup() {
    const { $auth } = useContext();
    const route = useRoute();

    const loggedIn = computed(() => {
      return $auth.loggedIn;
    });

    const groupSlug = route.value.params.groupSlug;
    const routerLink = !loggedIn.value && groupSlug ? `/explore/recipes/${groupSlug}` : "/"
    const domSearchDialog = ref<InstanceType<typeof RecipeDialogSearch> | null>(null);

    function activateSearch() {
      domSearchDialog.value?.open();
    }

    function handleKeyEvent(e: KeyboardEvent) {
      const activeTag = document.activeElement?.tagName;
      if (e.key === "/" && activeTag !== "INPUT" && activeTag !== "TEXTAREA") {
        e.preventDefault();
        activateSearch();
      }
    }

    onMounted(() => {
      document.addEventListener("keydown", handleKeyEvent);
    });

    onBeforeUnmount(() => {
      document.removeEventListener("keydown", handleKeyEvent);
    });

    return {
      activateSearch,
      domSearchDialog,
      routerLink,
    };
  },
});
</script>
