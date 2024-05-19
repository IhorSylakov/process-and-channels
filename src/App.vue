<template>
  <div class="app">
    <ul class="process-list">
      <li
        v-for="process in processList"
        :key="process.id"
        :class="['process-item', {'is-active': activeProcess === process.name}]"
      >
        <button
          class="process-button"
          @click="setProcessType(process.name)"
        >
          <img
            height="60"
            width="75"
            :alt="process.name"
            :src="require(`./assets/${process.name}.svg`)"
          >
        </button>
      </li>
      <li class="process-item">
        <input
          type="number"
          min="1"
          max="10"
          v-model.number="channelsCount"
          @input="updateChannelsCount"
          @keydown="preventOutOfRangeInput"
        >
      </li>
    </ul>
    <MainComponent
      :processType="activeProcess"
      :channels="channelsList"
    />
  </div>
</template>

<script>
  import MainComponent from './components/MainComponent.vue'

  export default {
    name: 'App',
    components: {
      MainComponent
    },
    data() {
      return {
        processList: [
          {
            id: 1,
            name: 'ongoing',
          },
          {
            id: 3,
            name: 'linear',
          },
          {
            id: 2,
            name: 'non-linear',
          },
          {
            id: 3,
            name: 'bidirectional',
          },
        ],
        channelsCount: 3,
        activeProcess: 'ongoing'
      }
    },
    computed: {
      channelsList() {
        const array = [];
        for (let i = 1; i <= this.channelsCount; i++) {
          array.push({
            id: i,
            text: `test${i}`,
          });
        }

        return array;
      }
    },
    methods: {
      setProcessType(processType) {
        this.activeProcess = processType;
      },
      updateChannelsCount(event) {
        this.channelsCount = event.target.value;
      },
      preventOutOfRangeInput(event) {
        const inputValue = parseInt(event.target.value);
        if (inputValue < 1) {
          this.channelsCount = 1;
        } else if (inputValue > 10) {
          this.channelsCount = 10;
        }
      },
    }
}
</script>

<style lang="scss">
  *,
  *::after,
  *::before {
    box-sizing: border-box;
  }

  :root {
    --system-theme: enable;

    container-name: html;
  }

  body {
    --theme-color: #000;
    --theme-bg-color: #fff;

    color: var(--theme-color);
    background: var(--theme-bg-color);
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;

    @container html style(--system-theme: enable) {
      @media (prefers-color-scheme: light) {
        --theme-color: #000;
        --theme-bg-color: #fff;
      }

      @media (prefers-color-scheme: dark) {
        --theme-color: #fff;
        --theme-bg-color: #000;
      }
    }
  }

  .process-list {
    margin: 0 0 20px;
    padding: 0;
    list-style: none;
    display: flex;
    gap: 15px;
  }

  .process-item {
    min-width: 100px;
    display: flex;
    align-items: center;
    justify-content: center;
    border: 1px solid var(--theme-color);

    &.is-active {
      background: lightgreen;
    }

    input {
      font-size: 24px;
    }
  }

  .process-button {
    margin: 0;
    border: 0;
    padding: 10px;
    background: transparent;
  }
</style>
