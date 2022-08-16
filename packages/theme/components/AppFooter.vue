<template>
  <SfFooter :column="4" multiple class="footer">
    <SfFooterColumn :title="$t('About us')">
      <SfList>
        <SfListItem>
            <router-link to="/page/about" exact>
              <SfMenuItem
                class="sf-footer__menu-item"
                :label="$t('Who we are')"
              />
            </router-link>
          </SfListItem>
          <SfListItem>
            <router-link to="/page/quality" exact>
              <SfMenuItem
                class="sf-footer__menu-item"
                :label="$t('Quality in the details')"
              />
            </router-link>
          </SfListItem>
          <SfListItem>
            <router-link to="/page/reviews" exact>
              <SfMenuItem
                class="sf-footer__menu-item"
                :label="$t('Customer reviews')"
              />
            </router-link>
          </SfListItem>
      </SfList>
    </SfFooterColumn>
    <SfFooterColumn :title="$t('Departments')">
      <SfList>
        <SfListItem
          v-for="item in departments"
          :key="item"
        >
          <SfMenuItem
            :label="$t(item)"
          />
        </SfListItem>
      </SfList>
    </SfFooterColumn>
    <SfFooterColumn :title="$t('Help')">
      <SfList>
        <SfListItem>
            <router-link to="/page/customer" exact>
              <SfMenuItem
                class="sf-footer__menu-item"
                :label="$t('Customer service')"
              />
            </router-link>
          </SfListItem>
          <SfListItem>
            <router-link to="/page/size" exact>
              <SfMenuItem
                class="sf-footer__menu-item"
                :label="$t('Size guide')"
              />
            </router-link>
          </SfListItem>
          <SfListItem>
            <router-link to="/page/contact" exact>
              <SfMenuItem
                class="sf-footer__menu-item"
                :label="$t('Contact us')"
              />
            </router-link>
          </SfListItem>
      </SfList>
    </SfFooterColumn>
    <SfFooterColumn :title="$t('Payment & Delivery')">
      <SfList>
        <SfListItem>
            <router-link to="/page/terms" exact>
              <SfMenuItem
                class="sf-footer__menu-item"
                :label="$t('Purchase terms')"
              />
            </router-link>
          </SfListItem>
          <SfListItem>
            <router-link to="/page/guarantee" exact>
              <SfMenuItem
                class="sf-footer__menu-item"
                :label="$t('Guarantee')"
              />
            </router-link>
          </SfListItem>
      </SfList>
    </SfFooterColumn>
    <SfFooterColumn title="Social">
      <div class="footer__socials">
        <SfImage class="footer__social-image" v-for="item in social" :key="item" :src="'/icons/'+item+'.svg'" :alt="item" width="32" height="32" />
      </div>
    </SfFooterColumn>
        <SfFooterColumn class="desktop-only" title="Language">
      <button
        class="sf-button--pure sf-button"
        :aria-disabled="false"
        :link="null"
        type="button"
        aria-label="button"
      >
        <SfImage
          src="/assets/storybook/SfFooter/flag.png"
          alt="flag of the USA"
          :width="20"
          :height="20"
        />
        English
      </button>
    </SfFooterColumn>
        <SfFooterColumn class="desktop-only">
      <div style="display:flex">
        <SfInput
          class="sf-input--outline"
          type="text"
          placeholder="Type your email address"
          inner-width="100%"
        />
        <button
          class="sf-button"
          :aria-disabled="false"
          :link="null"
          type="button"
          aria-label="button"
        >
          Subscribe
        </button>
      </div>
    </SfFooterColumn>
  </SfFooter>
</template>

<script>
import { SfFooter, SfList, SfImage, SfMenuItem, SfInput } from '@storefront-ui/vue';
export default {
  components: {
    SfFooter,
    SfList,
    SfImage,
    SfMenuItem,
   SfInput
  },
  data() {
    return {
      departments: ['Women fashion', 'Men fashion', 'Kidswear', 'Home'],
      social: ['facebook', 'pinterest', 'google', 'twitter', 'youtube'],
      isMobile: false,
      desktopMin: 1024
    };
  }
};
</script>

<style lang="scss">
.footer {
  margin-bottom: 3.75rem;
  @include for-desktop {
    margin-bottom: 0;
  }
  &__socials {
    display: flex;
    justify-content: space-between;
    margin: 0 auto var(--spacer-lg);
    padding: var(--spacer-base) var(--spacer-xl);
    @include for-desktop {
      justify-content: flex-start;
      padding: var(--spacer-xs) 0;
      margin: 0 auto;
    }
  }
  &__social-image {
    margin: 0 var(--spacer-2xs) 0 0;
  }
}
.sf-footer {
  @include for-desktop {
    border-top: var(--spacer-xs) solid var(--c-primary);
    padding-bottom: 0;
    margin-top: var(--spacer-2xl);
  }
  &__container {
    margin: var(--spacer-sm);
    @include for-desktop {
      max-width: 69rem;
      margin: 0 auto;
    }
  }
}
</style>
