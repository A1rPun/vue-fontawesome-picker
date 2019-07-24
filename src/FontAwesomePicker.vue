<template>
  <div class="fontawesome-picker">
    <input
      class="fontawesome-picker__input"
      type="text"
      v-model="iconClassName"
      @click="showPicker"
    >
    <div v-show="picker" class="fontawesome-picker__picker">
      <span
        class="fontawesome-picker__icon"
        v-for="(icon, idx) in displayList"
        :key="idx"
        :class="{ 'fontawesome-picker__icon--selected': appIcon.length && appIcon[0] === icon.prefix && appIcon[1] === icon.iconName }"
        @click="closePicker(icon)"
      >
        <font-awesome-icon
          :icon="[icon.prefix, icon.iconName]"
          :title="icon.iconName"
        />
      </span>
    </div>
  </div>
</template>

<script>
import { library } from '@fortawesome/fontawesome-svg-core';
import { fas } from '@fortawesome/free-solid-svg-icons';
import { far } from '@fortawesome/free-regular-svg-icons';
import { fab } from '@fortawesome/free-brands-svg-icons';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';

delete fas.faFontAwesomeLogoFull;
delete far.faFontAwesomeLogoFull;
delete fab.faFontAwesomeLogoFull;

library.add(fas, far, fab);

export default {
  components: {
    FontAwesomeIcon,
  },
  props: {
    value: {
      type: String,
      default: '',
    },
  },
  computed: {
    displayList() {
      return this.icons
        .filter(x => !this.filter || x.iconName.includes(this.filter))
        .sort((a, b) => a.iconName.localeCompare(b.iconName));
    },
    icons() {
      return [...Object.values(fas), ...Object.values(far), ...Object.values(fab)];
    },
  },
  watch: {
    value(val) {
      let [prefix, iconName] = val.split(' ');
      if (iconName) iconName = iconName.slice('fa-'.length);
      this.setValue({ prefix, iconName });
    },
  },
  data() {
    return {
      picker: false,
      appIcon: [],
      iconClassName: '',
      filter: '',
    };
  },
  methods: {
    setValue(icon) {
      this.appIcon = [icon.prefix, icon.iconName];
      this.iconClassName = `${icon.prefix} fa-${icon.iconName}`;
    },
    showPicker() {
      this.picker = true;
    },
    closePicker(icon) {
      this.setValue(icon);
      this.$emit('input', this.iconClassName);
      this.picker = false;
    },
  },
};
</script>

<style lang="scss">
$vue-color: #2c3e50;
$vue-alt-color: #42b983;

.fontawesome-picker {
  width: 200px;
  box-sizing: border-box;
}

.fontawesome-picker__input {
  width: 200px;
  border: 1px solid $vue-color;
  background-color: white;
  color: $vue-color;
  box-sizing: border-box;
}

.fontawesome-picker__picker {
  max-height: 200px;
  border: 1px solid $vue-color;
  border-top: none;
  overflow-x: hidden;
  display: flex;
  flex-flow: row wrap;
  justify-content: space-between;
}

.fontawesome-picker__icon {
  display: inline-block;
  width: 32px;
  height: 32px;
  border-radius: 16px;
  text-align: center;
  line-height: 32px;
  color: $vue-color;
  transition: transform 0.2s;

  &:hover {
    background-color: $vue-alt-color;
    color: white;
    transform: scale(1.5);
  }
}

.fontawesome-picker__icon--selected {
  background-color: #e4f5ef;
  color: $vue-alt-color;
}
</style>
