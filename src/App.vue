<script setup lang="ts">
import { ref, reactive, computed } from "vue";
import ConfettiExplosion from "vue-confetti-explosion";
import { tags, type Tag, TagDescription } from "./tags";

const newTag = ref("");

const tagsFound: Tag[] = reactive([]);
const hints: Record<Tag, number> = reactive({});
const remaining = computed(() =>
  tags.filter((tag) => !tagsFound.includes(tag))
);

function tryTag() {
  if (
    tags.includes(newTag.value as Tag) &&
    !tagsFound.includes(newTag.value as Tag)
  ) {
    tagsFound.push(newTag.value as Tag);
  }
  newTag.value = "";
}

function getHint(tag: Tag) {
  if (tag && !tagsFound.includes(tag)) {
    hints[tag] = (hints[tag] ?? 0) + 1;
    if (hints[tag] === tag.length) {
      tagsFound.push(tag);
    }
  }
}
</script>

<template>
  <header>
    <h1>HTML Finder: Find all the HTML tags!</h1>
  </header>

  <main>
    <section>
      <header>
        <h2>
          {{ tagsFound.length }} tags found, {{ remaining.length }} remaining
        </h2>
        <label style="float: right"
          >Enter a HTML tag:
          <input type="text" v-model="newTag" @keypress.enter="tryTag" />
        </label>
        <ConfettiExplosion v-if="remaining.length === 0" />
      </header>
      <ul id="to-find">
        <li v-for="tag in tags" :key="tag" @click="getHint(tag)">
          <span class="tag" v-if="tagsFound.includes(tag)">{{ tag }}</span>
          <template v-else>
            <span class="tag-hint">
              {{ tag.slice(0, hints[tag] ?? 0) }}
            </span>
            <span class="tag-not-found">{{
              tag.slice(hints[tag] ?? 0).replace(/\w/g, "_")
            }}</span>
          </template>
        </li>
      </ul>
    </section>

    <section id="last-found">
      <h2>Last found</h2>
      <dl>
        <template v-for="tag in tagsFound.reverse()" :key="tag">
          <dt>
            <span class="tag">{{ tag }}</span>
          </dt>
          <dd>{{ TagDescription[tag] }}</dd>
        </template>
      </dl>
    </section>
  </main>
</template>

<style scoped>
h1 {
  margin-bottom: 0.5em;
}

h2 {
  margin: 0;
}

main {
  display: grid;
  grid-template-columns: 1fr 480px;
}
#to-find {
  columns: 8rem auto;
}

#to-find li {
  list-style: none;
}

#last-found {
  border-left: 4px solid #ddd;
  margin-left: 0.5em;
}

#last-found dl {
  display: grid;
  grid-template-columns: 10ch 1fr;
  grid-gap: 1rem;
  max-height: calc(100vh - 14rem);
  overflow: auto;
}

#last-found dd {
  font-size: 80%;
  text-align: left;
}

section header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  align-content: center;
}

.tag {
  color: blue;
}
.tag::before {
  content: "<";
  color: gray;
}
.tag::after {
  content: ">";
  color: gray;
}

.tag-not-found {
  font-family: "Roboto Mono", monospace;
  color: gray;
}

.tag-hint {
  font-family: "Roboto Mono", monospace;
  color: red;
}
</style>
