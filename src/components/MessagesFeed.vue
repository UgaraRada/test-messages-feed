<template>
  <div>
    <div
      v-for="it in dataList"
      :key="it.index"
      class="message"
    >
      <div class="mb-4">
        {{ parseDate(it.date) }}
        / <b>{{ it.authorName }}</b> /
        <a
          class="message__link"
          :href="it.authorUrl"
        >
          {{ it.authorUrl }}
        </a>
      </div>
      <p v-html="highlightText(it.content, it.contentPostTones)" />
    </div>
  </div>
</template>

<script>
import dataList from '@/utils/feed.json';

export default {
  name: 'MessagesFeed',
  data() {
    return {
      dataList,
      dateOptions: {
        year: 'numeric',
        month: 'long',
        day: 'numeric',
      },
      timeOptions: {
        hour: '2-digit',
        minute: '2-digit',
      },
    };
  },
  methods: {
    getColor(num) {
      const sign = Math.sign(num);
      let green;
      let red;

      if (sign <= 0) {
        green = (1 - Math.abs(num)) * 255;
        red = 255;
      } else {
        green = 255;
        red = (1 - Math.abs(num)) * 255;
      }

      return `rgb(${red}, ${green}, 0)`;
    },
    highlightText(content, contentPostTones) {
      let replacementLength = 0;
      const highlightMatch = (contentLocal, { position, tone, length }) => {
        const newPosition = position + replacementLength;
        const substr = contentLocal.substr(newPosition, length);
        const replacement = `<span style="background-color:${this.getColor(tone)}">${substr}</span>`;
        const template = contentLocal.substr(0, newPosition) + replacement + contentLocal.substr(newPosition + length);

        replacementLength += replacement.length - length;
        return template;
      };
      return contentPostTones.reduce((acc, it) => highlightMatch(acc, it), content);
    },
    parseDate(it) {
      const date = new Date(it).toLocaleString('ru', this.dateOptions);
      const time = new Date(it).toLocaleString('ru', this.timeOptions);

      return `${time}, ${date}`;
    },
  },
};
</script>

<style lang="scss" scoped>
.message {
  border-bottom: 1px solid rgba(0, 0, 0, 0.2);
  margin-bottom: 16px;

  &__link {
    color: inherit;
    text-decoration: none;
  }
}
</style>
