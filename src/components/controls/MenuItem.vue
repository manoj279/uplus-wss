<template>
  <li>
    <a v-on:click="gotoPage" :href="href !== '' ? href : ''">{{ $t("message." + title) }}</a>
  </li>
</template>

<script>
import { mainconfig, i18n } from '../../global';
import { sendClickStreamEvent } from '../../CDHIntegration';

export default {
  props: ['title', 'href', 'pagetype'],
  data() {
    return { mainconfig, i18n };
  },
  methods: {
    gotoPage(event) {
      mainconfig.currentPage = this.href;
      if (this.$gtag) {
        this.$gtag.pageview({
          page_path: mainconfig.currentPage,
        });
      }
      if (mainconfig.currentPage.indexOf('offer') === 0) {
        mainconfig.offerIndex = parseInt(mainconfig.currentPage.substring(5).replace('.html', ''), 10);
      }
      sendClickStreamEvent(mainconfig, 'PageView', this.pagetype, window.loadPage);
      window.loadPage = new Date();
      const stateObj = mainconfig.isAuthenticated ? { userId: mainconfig.userId } : {};
      window.history.pushState(stateObj, '', mainconfig.currentPage);
      // document.title = `${mainconfig.mainTitle}-${i18n.t(`message.${this.title}`)}`;
      window.scrollTo({ top: 0, behavior: 'smooth' });
      event.preventDefault();
    },
  },
};
</script>
