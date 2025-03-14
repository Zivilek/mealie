<template>
  <div v-if="items.length > 0">
    <h2 v-if="title" class="mt-4">{{ title }}</h2>
    <v-chip
      v-for="category in items.slice(0, limit)"
      :key="category.name"
      label
      class="ma-1"
      color="accent"
      :small="small"
      dark
      :to=" loggedIn ? `/?${urlPrefix}=${category.id}` : undefined"
    >
      {{ truncateText(category.name) }}
    </v-chip>
  </div>
</template>

<script lang="ts">
import { computed, defineComponent, useContext } from "@nuxtjs/composition-api";
import { RecipeCategory, RecipeTag, RecipeTool } from "~/lib/api/types/user";

export type UrlPrefixParam = "tags" | "categories" | "tools";

export default defineComponent({
  props: {
    truncate: {
      type: Boolean,
      default: false,
    },
    items: {
      type: Array as () => RecipeCategory[] | RecipeTag[] | RecipeTool[],
      default: () => [],
    },
    title: {
      type: Boolean,
      default: false,
    },
    urlPrefix: {
      type: String as () => UrlPrefixParam,
      default: "categories",
    },
    limit: {
      type: Number,
      default: 999,
    },
    small: {
      type: Boolean,
      default: false,
    },
    maxWidth: {
      type: String,
      default: null,
    },
  },
  setup(props) {
    const { $auth } = useContext();
    const loggedIn = computed(() => {
      return $auth.loggedIn
    })

    function truncateText(text: string, length = 20, clamp = "...") {
      if (!props.truncate) return text;
      const node = document.createElement("div");
      node.innerHTML = text;
      const content = node.textContent || "";
      return content.length > length ? content.slice(0, length) + clamp : content;
    }

    return {
      loggedIn,
      truncateText,
    };
  },
});
</script>

<style></style>
