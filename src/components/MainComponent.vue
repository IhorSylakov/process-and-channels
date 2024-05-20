<template>
  <div
    class="container"
    :style="processType !== 'ongoing' ? 'min-height: 320px;' : ''"
  >
    <div
      class="process-and-channels"
      :style="getGeneralStyles"
    >
      <div
        :class="['process', `process--${processType}`]"
        :style="getNonLinearStyles()"
      >
        <div
          v-for="channel in channels"
          :key="channel.id"
          :class="[ 'channel', `channel--${processType}` ]"
          :style="getOngoingStyles(channel.id)"
        >
          <div class="channel__item">
            <div class="channel__item-icon">
              <img
                height="32"
                width="32"
                alt="channel type"
                src="../assets/blank-channel.svg"
              >
            </div>
          </div>

          <div
            class="channel__item-text"
            :style="getChannelVerticalPosition(channel.id)"
          >
            <div contenteditable="true">{{ channel.text }}</div>
          </div>
        </div>
      </div>
    </div>
    <div
      class="resizer-x"
      @mousedown="startDragX"
      @mousemove="doDragX"
      @mouseup="stopDragX"
    />
    <div
      v-if="processType === 'ongoing'"
      class="resizer-y"
      @mousedown="startDragY"
      @mousemove="doDragY"
      @mouseup="stopDragY"
    />
  </div>
</template>

<script>
  const containerMinWidth = 350;
  const containerMinHeight = 160;

  export default {
    name: 'MainComponent',
    props: {
      processType: {
        type: String,
        default: 'ongoing',
        validator: function(value) {
          return ['ongoing', 'linear', 'non-linear', 'bidirectional'].includes(value);
        }
      },
      channels: {
        type: Array,
        required: true,
      },
    },
    data() {
      return {
        isDragging: false,
        startX: 0,
        startY: 0,
        startWidth: 0,
        startHeight: 0,
        diametr: 200,
        containerWidth: 558,
      };
    },
    computed: {
      channelsCount() {
        // + 1 because arrow also take place on process line and affects calculations
        return this.channels.length + 1;
      },

      getGeneralStyles() {
        const width = `width: ${this.containerWidth}px;`;
        const computedStyles = `--min-width: ${this.containerWidth}px;`;
        const channelsCount = `--channels-count: ${this.channels.length};`;
        const radius = `--circle-radius: ${this.diametr / 2}px;`;

        return `${width} ${computedStyles} ${channelsCount} ${radius}`;
      },
    },
    methods: {
      ongoingChannelDegree(id) {
        const itemsCount = this.channelsCount;
        const angle = 360 / itemsCount;

        return angle * id - 90;
      },

      getChannelVerticalPosition(id) {
        const degree = this.ongoingChannelDegree(id);
        const temp = degree < 0 ? 360 + degree : degree;
        const posVertical = temp < 181 ? 'top: 100%' : 'bottom: 100%';
        let posHorizontal = 'left: -5%';

        if (degree > 90) {
          posHorizontal = 'right: 25%';
        }
        if (degree < 90) {
          posHorizontal = 'left: 25%';
        }

        return this.processType === 'ongoing' ? `${posVertical}; ${posHorizontal};` : '';
      },

      getOngoingStyles(id) {
        const degree = this.ongoingChannelDegree(id);
        const sin = Math.sin(degree * (Math.PI / 180)).toFixed(4);
        const cos = Math.cos(degree * (Math.PI / 180)).toFixed(4);

        return this.processType === 'ongoing' ? `--sin: ${sin}; --cos: ${cos};` : '';
      },

      getNonLinearStyles() {
        const itemHeight = 120;
        const sideIndents = 150;
        const minWidth = 50;
        const maxWidth = 150;
        const computedWidth = (this.containerWidth - sideIndents) / this.channelsCount;
        let itemWidth = computedWidth;

        if (computedWidth < minWidth) {
          itemWidth = minWidth;
        }
        if (computedWidth > maxWidth) {
          itemWidth = maxWidth;
        }

        // Pythagorean Theorema to find hypotenuse
        const hypotenuse = Math.sqrt(itemHeight ** 2 + itemWidth ** 2);

        // https://en.wikipedia.org/wiki/Right_triangle
        // required angle of right triangle is arctan of ratio width to height,
        // but Math.atan() return in radians, so we need convert it
        const degree = Math.atan(itemWidth / itemHeight) / (Math.PI / 180);

        return this.processType === 'non-linear' ? `--height: ${hypotenuse}px; --degree: ${degree}deg; --item-width: ${itemWidth}px;` : '';
      },

      startDragX(event) {
        this.isDragging = true;
        this.startWidth = this.containerWidth;
        this.startX = event.clientX;
        document.addEventListener('mousemove', this.doDragX);
        document.addEventListener('mouseup', this.stopDragX);
      },
      doDragX(event) {
        if (this.isDragging) {
          const deltaX = event.clientX - this.startX;
          this.containerWidth = Math.max(containerMinWidth, this.startWidth + deltaX);
        }
      },
      stopDragX() {
        this.isDragging = false;
        document.removeEventListener('mousemove', this.doDragX);
        document.removeEventListener('mouseup', this.stopDragX);
      },

      startDragY(event) {
        this.isDragging = true;
        this.startHeight = this.diametr;
        this.startY = event.clientY;
        document.addEventListener('mousemove', this.doDragY);
        document.addEventListener('mouseup', this.stopDragY);
      },
      doDragY(event) {
        if (this.isDragging) {
          const deltaY = event.clientY - this.startY;
          this.diametr = Math.max(containerMinHeight, this.startHeight + deltaY);
        }
      },
      stopDragY() {
        this.isDragging = false;
        document.removeEventListener('mousemove', this.doDragY);
        document.removeEventListener('mouseup', this.stopDragY);
      },
    },
  }
</script>

<style scoped lang="scss">
  .container {
    position: relative;
    display: inline-flex;
    align-items: center;
    border: 1px solid var(--theme-color);

    &::after {
      content: '';
      inset: 0;
      left: auto;
      z-index: 1;
      width: 25px;
      position: absolute;
      background: linear-gradient(to left, var(--theme-bg-color) 50%, transparent 100%);
    }
  }

  .resizer-x {
    position: absolute;
    bottom: -1px;
    right: -17px;
    top: -1px;
    display: flex;
    cursor: ew-resize;
    background-color: #bbb;
    user-select: none;

    &::after {
      content: '=';
      display: block;
      writing-mode: vertical-lr;
      text-align: center;
      line-height: 16px;
      font-size: 24px;
    }
  }

  .resizer-y {
    position: absolute;
    bottom: -17px;
    right: -1px;
    left: -1px;
    display: flex;
    justify-content: center;
    cursor: ns-resize;
    background-color: #bbb;
    user-select: none;

    &::after {
      content: '=';
      display: block;
      line-height: 16px;
      font-size: 24px;
    }
  }

  .process-and-channels {
    --stroke-color: lightcoral;
    --stroke-width: 8px;
    --side-paddings: 75px;
    --ongoing-koef: 0.8;
    --arrowSize: 32px;
    --channel-icon-size: 32px;

    padding: 40px var(--side-paddings) 10px;
    justify-content: center;
    overflow: hidden;
  }

  .process {
    &::after {
      content: '';
      position: absolute;
      height: var(--arrowSize);
      width: var(--arrowSize);
      border: solid var(--stroke-color);
      border-width: var(--stroke-width) var(--stroke-width) 0 0;
    }
  }

  .channel {
    &__item {
      position: relative;
      z-index: 1;
    }

    &__item-icon {
      width: var(--channel-icon-size);
      height: var(--channel-icon-size);
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
      font-size: 20px;
      font-weight: bold;
      border-radius: 50%;
      background: #fff;
    }

    &__item-text {
      position: absolute;
      margin: 5px 0;
      z-index: 1;
      max-height: 40px;
      width: 110%;
      min-width: 50px;
      max-width: 175px;
      line-height: 20px;
      overflow: hidden;
      text-align: center;
      display: flex;
      justify-content: center;
      align-items: flex-start;
      font-size: 16px;
      font-weight: 400;
      color: #000;

      [contenteditable="true"],
      [contenteditable] {
        background: rgba(#fff, 0.9);
        padding: 0 2px;
        user-select: initial;

        &:focus,
        &:hover {
          background: rgba(#fff, 0.6);
        }
      }
    }
  }

  .process--linear {
    align-self: center;
    display: flex;
    position: relative;
    width: 100%;
    height: var(--stroke-width);
    min-width: calc(20px + 80px * var(--channels-count));
    padding-right: 10px;
    margin: 32px 0 62px;
    background: var(--stroke-color);
    border-radius: 4px;

    &::after {
      right: 0;
      top: calc(-0.5 * (var(--channel-icon-size) - var(--stroke-width)));
      transform: rotate(45deg);
    }
  }

  .channel--linear {
    position: relative;
    width: calc(100% / var(--channels-count));

    .channel__item {
      margin-top: calc(-0.5 * (var(--channel-icon-size) - var(--stroke-width)));
    }

    .channel__item-text {
      width: 100%;
      transform: translateX(calc(-50% + var(--channel-icon-size) / 2));
    }

    &:first-child:last-child {
      margin-inline: auto;
      max-width: 50%;

      .channel__item-icon {
        margin-inline: auto;
      }

      .channel__item-text {
        left: 50%;
        transform: translateX(-50%);
      }
    }

    &:nth-child(even) .channel__item-text {
      bottom: calc(100% + 0.5 * (var(--channel-icon-size) - var(--stroke-width)));
    }

    &:nth-child(odd) .channel__item-text {
      top: calc(100% + 0.5 * (var(--channel-icon-size) - var(--stroke-width)));
    }
  }

  .process--bidirectional {
    align-self: center;
    display: flex;
    position: relative;
    width: 100%;
    height: var(--stroke-width);
    min-width: calc(20px + 80px * var(--channels-count));
    padding: 0 10px;
    margin: 32px 0 62px;
    background: var(--stroke-color);
    border-radius: 4px;

    &::before {
      content: '';
      position: absolute;
      height: var(--arrowSize);
      width: var(--arrowSize);
      border: solid var(--stroke-color);
      border-width: var(--stroke-width) var(--stroke-width) 0 0;
      left: -5px;
      top: calc(-0.5 * (var(--channel-icon-size) - var(--stroke-width)));
      transform: rotate(225deg);
    }

    &::after {
      right: -5px;
      top: calc(-0.5 * (var(--channel-icon-size) - var(--stroke-width)));
      transform: rotate(45deg);
    }
  }

  .channel--bidirectional {
    display: flex;
    flex-direction: column;
    align-items: center;
    position: relative;
    width: calc(100% / var(--channels-count));

    .channel__item {
      margin-top: calc(-0.5 * (var(--channel-icon-size) - var(--stroke-width)));
    }

    .channel__item-text {
      width: 100%;
    }

    &:first-child:last-child {
      margin-inline: auto;
      max-width: 90%;

      .channel__item-text {
        transform: none;
      }
    }

    &:nth-child(even) .channel__item-text {
      bottom: calc(100% + 0.5 * (var(--channel-icon-size) - var(--stroke-width)));
    }

    &:nth-child(odd) .channel__item-text {
      top: calc(100% + 0.5 * (var(--channel-icon-size) - var(--stroke-width)));
    }
  }

  .process--ongoing {
    position: relative;
    width: 80%;
    min-width: calc(((var(--min-width) - var(--side-paddings) * 2) * var(--ongoing-koef)));
    height: calc(var(--circle-radius) * 2);
    margin: 10px auto 50px;

    &::before {
      border: var(--stroke-width) solid var(--stroke-color);
      border-radius: 100%;
      content: '';
      min-width: calc(((var(--min-width) - var(--side-paddings) * 2) * var(--ongoing-koef)));
      height: calc(var(--circle-radius) * 2);
      position: absolute;
      top: 0;
      left: 0;
    }

    &::after {
      --sinArrow: -1;
      --cosArrow: 0;
      --xr: calc(((var(--min-width) - var(--side-paddings) * 2) * var(--ongoing-koef)) / 2);
      --y1: calc(var(--sinArrow) * (var(--circle-radius) - var(--stroke-width)));
      --x1: calc(var(--cosArrow) * (var(--xr) - var(--stroke-width)));

      left: 50%;
      top: 50%;
      margin-top: calc(-1 * (var(--arrowSize) + var(--stroke-width)) / 2);
      margin-left: calc(-1 * (var(--arrowSize) + var(--stroke-width)) / 2);
      transform: translateX(var(--x1)) translateY(var(--y1)) rotate(45deg);
    }
  }

  .channel--ongoing {
    --xr: calc(((var(--min-width) - var(--side-paddings) * 2) * var(--ongoing-koef)) / 2);
    --y1: calc(var(--sin) * (var(--circle-radius) - var(--stroke-width)));
    --x1: calc(var(--cos) * (var(--xr) - var(--stroke-width)));

    position: absolute;
    width: 20%;
    display: flex;
    align-items: center;
    flex-direction: column;
    left: 50%;
    top: 50%;
    margin-top: -16px;
    margin-left: -10%;
    transform: translateX(var(--x1)) translateY(var(--y1));
  }

  .process--non-linear {
    display: flex;
    height: 120px;
    position: relative;
    padding-right: var(--item-width);
    width: calc(var(--item-width) * (var(--channels-count) + 1));
    margin: 30px auto 60px;

    &::before {
      border-radius: var(--stroke-width);
      content: '';
      width: var(--stroke-width);
      height: var(--height);
      background: var(--stroke-color);
      transform: rotate(calc(-1 * var(--degree)));
      position: absolute;
      bottom: 0;
      right: 0;
      transform-origin: right bottom;
    }

    &::after {
      right: 0;
      bottom: 0;
      margin-bottom: -6px;
      transform: rotate(calc(45deg - var(--degree)));
      transform-origin: right bottom;
      border-width: 0 var(--stroke-width) var(--stroke-width) 0;
    }
  }

  .channel--non-linear {
    position: relative;
    height: 100%;
    width: var(--item-width);
    display: flex;
    flex-direction: column;
    align-items: center;

    .channel__item {
      left: 50%;
      margin: calc(-1 * var(--stroke-width)) 0 0;
    }

    .channel__item-text {
      left: 45%;
      bottom: calc(100% + var(--stroke-width));
    }

    &::before {
      border-radius: var(--stroke-width);
      content: '';
      width: var(--stroke-width);
      height: var(--height);
      background: var(--stroke-color);
      transform: rotate(var(--degree));
      position: absolute;
      bottom: 0;
      left: 0;
      transform-origin: bottom left;
    }

    &:nth-last-child(2n) {
      justify-content: flex-end;

      .channel__item {
        margin: 0 0 -16px;
      }

      .channel__item-text {
        bottom: auto;
        top: calc(100% + 16px);
      }

      &::before {
        left: auto;
        right: 0;
        bottom: 0;
        transform: rotate(calc(-1 * var(--degree)));
        transform-origin: right bottom;
      }
    }
  }
</style>
