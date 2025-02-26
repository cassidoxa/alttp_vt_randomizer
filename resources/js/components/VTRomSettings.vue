<template>
  <div>
    <div class="row mb-3">
      <div class="col-md-12">
        <vt-select
          id="heart-speed"
          :options="settings.heartSpeeds"
          storage-key="rom.heart-speed"
          :rom="rom"
          :selected="defaults.heartSpeeds"
          rom-function="setHeartSpeed"
        >{{ $t('rom.settings.heart_speed') }}</vt-select>
      </div>
    </div>
    <div class="row mb-3">
      <div class="col-md-12">
        <vt-sprite-select
          id="sprite-gfx"
          :rom="rom"
          storage-key="rom.sprite-gfx"
          :title="$t('rom.settings.play_as')"
          @load-custom-sprite="loadCustomSprite"
        ></vt-sprite-select>
      </div>
    </div>
    <div v-if="showLoadCustomSprite" class="row mb-3">
      <div class="col-md-12">
        <vt-sprite-loader
          id="sprite-loader"
          :rom="rom"
          storage-key="rom.custom-sprite-gfx"
          @disallow-save-rom="disallowSaveRom"
        ></vt-sprite-loader>
      </div>
    </div>
    <div v-if="!rom.tournament" class="row mb-3">
      <div class="col-md-12">
        <vt-select
          id="menu-speed"
          :options="settings.menuSpeeds"
          storage-key="rom.menu-speed"
          :rom="rom"
          :selected="defaults.menuSpeeds"
          rom-function="setMenuSpeed"
        >{{ $t('rom.settings.menu_speed') }}</vt-select>
      </div>
    </div>
    <div class="row mb-3">
      <div class="col-md-12">
        <vt-select
          id="heart-color"
          :options="settings.heartColors"
          storage-key="rom.heart-color"
          :rom="rom"
          :selected="defaults.heartColors"
          rom-function="setHeartColor"
        >{{ $t('rom.settings.heart_color') }}</vt-select>
      </div>
    </div>
    <div v-if="!rom.music" class="row mb-3">
      <div class="col">
        <Toggle :value="musicOn" @input="setMusicOn">{{ $t('rom.settings.music') }}</Toggle>
      </div>
      <div class="col">
        <Toggle :value="msu1Resume" @input="setMSU1Resume">{{ $t('rom.settings.msu1resume') }}</Toggle>
      </div>
    </div>
    <div class="row mb-3">
      <div v-if="!rom.tournament || rom.allowQuickSwap" class="col">
        <Toggle :value="quickswap" @input="setQuickswap">{{ $t('rom.settings.quickswap') }}</Toggle>
      </div>
      <div v-if="rom.build >= '2021-05-04'" class="col">
        <Toggle
          :value="reduceFlashing"
          @input="setReduceFlashing"
        >{{ $t('rom.settings.reduce_flashing') }}
        <sup
          v-if="reduceFlashing"
        >*</sup></Toggle>
      </div>
    </div>
    <div class="row mb-3">
      <div v-if="!rom.special" class="col">
        <Toggle
          :value="paletteShuffle"
          @input="setPaletteShuffle"
        >{{ $t('rom.settings.palette_shuffle') }}</Toggle>
      </div>
      <div v-if="!rom.music" class="col">
        <Toggle :value="shuffleSfx" @input="setShuffleSfx">{{ $t('rom.settings.shuffle_sfx') }}</Toggle>
      </div>
    </div>
    <div class="row mb-3">
    </div>
    <div
      v-if="reduceFlashing"
      class="logic-warning text-info"
      v-html="'* ' + $t('rom.settings.reduce_flashing_warning')"
    />
  </div>
</template>

<script>
import Toggle from "../components/Toggle.vue";
import { mapMutations, mapState } from "vuex";

export default {
  components: {
    Toggle
  },
  props: ["rom"],
  data() {
    return {
      showLoadCustomSprite: false,
      settings: {
        heartSpeeds: [
          { value: "off", name: this.$i18n.t("rom.settings.heart_speeds.off") },
          {
            value: "double",
            name: this.$i18n.t("rom.settings.heart_speeds.double")
          },
          {
            value: "normal",
            name: this.$i18n.t("rom.settings.heart_speeds.normal")
          },
          {
            value: "half",
            name: this.$i18n.t("rom.settings.heart_speeds.half")
          },
          {
            value: "quarter",
            name: this.$i18n.t("rom.settings.heart_speeds.quarter")
          }
        ],
        menuSpeeds: [
          {
            value: "instant",
            name: this.$i18n.t("rom.settings.menu_speeds.instant")
          },
          {
            value: "fast",
            name: this.$i18n.t("rom.settings.menu_speeds.fast")
          },
          {
            value: "normal",
            name: this.$i18n.t("rom.settings.menu_speeds.normal")
          },
          { value: "slow", name: this.$i18n.t("rom.settings.menu_speeds.slow") }
        ],
        heartColors: [
          {
            value: "blue",
            name: this.$i18n.t("rom.settings.heart_colors.blue")
          },
          {
            value: "green",
            name: this.$i18n.t("rom.settings.heart_colors.green")
          },
          {
            value: "red",
            name: this.$i18n.t("rom.settings.heart_colors.red")
          },
          {
            value: "yellow",
            name: this.$i18n.t("rom.settings.heart_colors.yellow")
          },
          {
            value: "random",
            name: this.$i18n.t("rom.settings.heart_colors.random")
          },
        ]
      },
      defaults: {
        heartSpeeds: {
          value: "half",
          name: this.$i18n.t("rom.settings.heart_speeds.half")
        },
        menuSpeeds: {
          value: "normal",
          name: this.$i18n.t("rom.settings.menu_speeds.normal")
        },
        heartColors: {
          value: "red",
          name: this.$i18n.t("rom.settings.heart_colors.red")
        },
        quickswap: false,
        music: true
      }
    };
  },
  methods: {
    loadCustomSprite(e) {
      this.showLoadCustomSprite = Boolean(e);
      this.$emit('disallow-save-rom', Boolean(e));
    },
    disallowSaveRom(e) {
      this.$emit('disallow-save-rom', Boolean(e));
    },
    ...mapMutations("romSettings", [
      "setHeartSpeed",
      "setMenuSpeed",
      "setHeartColor",
      "setQuickswap",
      "setMusicOn",
      "setMSU1Resume",
      "setPaletteShuffle",
      "setReduceFlashing",
      "setShuffleSfx",
    ])
  },
  computed: {
    ...mapState("romSettings", {
      optionsHeartSpeed: state => state.options.heartSpeed,
      heartSpeed: state => state.heartSpeed,
      optionsMenuSpeed: state => state.options.menuSpeed,
      menuSpeed: state => state.menuSpeed,
      optionsHeartColor: state => state.options.heartColor,
      heartColor: state => state.heartColor,
      quickswap: state => state.quickswap,
      musicOn: state => state.musicOn,
      msu1Resume: state => state.msu1Resume,
      paletteShuffle: state => state.paletteShuffle,
      reduceFlashing: state => state.reduceFlashing,
      shuffleSfx: state => state.shuffleSfx
    })
  }
};
</script>
