<template>
  <div class="faq">
    <div @click="toggle" class="question">
      {{ question }}
    </div>
    <div v-if="visible" class="answer">
      <slot />
    </div>
    <div @click="toggle"  v-if="!visible" class="icon">
      <img src="/icons/dropdown.svg" />
    </div>
    <div @click="toggle"  v-else class="icon down">
      <img src="/icons/dropdown.svg" />
    </div>
  </div>
</template>

<script lang="ts">
import { Vue, Component, Prop } from "nuxt-property-decorator";
import {ComponentName} from "@/constants/componentName";

@Component({
  name: ComponentName.Faq,
})
export default class extends Vue {
  @Prop({ required: true }) question!: string;
  visible = false;

  toggle() {
    // next action is visible
    if (!this.visible) {
      this.$track('faq', this.question);
    }

    this.visible = !this.visible;
  }
}
</script>

<style lang="scss" scoped>
@import "@/assets/colors";
@import "@/assets/scales";

.faq {
  padding-top: $gridsize/2;
  padding-bottom: $gridsize/2;

  display: grid;
  width: 100%;
  grid-template-columns: auto $gridsize/2;

  border-top: 1px solid $border;
  border-bottom: 1px solid $border;

  .question {
    grid-column: 1;
    grid-row: 1;
    font-size: $h3FontSize;
    color: $headercolor;
    font-weight: normal;
  }

  .icon {
    grid-column: 2;
    grid-row: 1;
  }

  .question,
  .icon {
    cursor: pointer;

    &:hover {
      color: $primary;
    }
  }

  .answer {
    padding-top: $gridsize/2;
    grid-row: 2;
    grid-column: 1 / span 2;
  }

  .down {
    img {
      transform: rotate(180deg);
    }
  }
}

.faq + .faq {
  border-top: 0;
}
</style>
