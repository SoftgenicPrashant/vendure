<template>
  <SfFooter :column="4" multiple class="footer">
    <SfFooterColumn :title="$t('Top Rated Products')">
      <SfList>
        <SfListItem>
            <router-link to="/page/about" exact>
              <SfMenuItem
                class="sf-footer__menu-item"
                :label="$t('My Orders')"
              />
            </router-link>
          </SfListItem>
          <SfListItem>
            <router-link to="/page/quality" exact>
              <SfMenuItem
                class="sf-footer__menu-item"
                :label="$t('Returns, Claims & Recalls')"
              />
            </router-link>
          </SfListItem>
          <SfListItem>
            <router-link to="/page/reviews" exact>
              <SfMenuItem
                class="sf-footer__menu-item"
                :label="$t('My Invoices')"
              />
            </router-link>
          </SfListItem>
            <SfListItem>
            <router-link to="/page/reviews" exact>
              <SfMenuItem
                class="sf-footer__menu-item"
                :label="$t('Recieving')"
              />
            </router-link>
          </SfListItem>
           <SfListItem>
            <router-link to="/page/reviews" exact>
              <SfMenuItem
                class="sf-footer__menu-item"
                :label="$t('Settings & Preferences')"
              />
            </router-link>
          </SfListItem>
      </SfList>
    </SfFooterColumn>
    <SfFooterColumn :title="$t('Resources')">
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
          src="/icons/flag.png"
          alt="flag of the USA"
          :width="20"
          :height="20"
        />
        English
      </button>
    </SfFooterColumn>
    <SfFooterColumn class="desktop-only" >
      <router-link to="/" exact>
      <img src="/icons/flogo.png" 
      />
      </router-link>
      <br></br>
      <div style="display:flex;" class="subscribe-mail">
        <SfInput
          class="sf-input--outline"
          type="text"
          placeholder="Type your email address"
          
        />


      </div>
    </SfFooterColumn>
    <SfFooterColumn class="desktop-only subscribe-btn" >
        <button
          class="sf-button"
          :aria-disabled="false"
          :link="null"
          type="button"
          aria-label="button"
          
          
        >
          Subscribe
        </button>
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
    border-top: var(--spacer-xs) solid #6C227E;
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
 .subscribe-mail .sf-input input{
  height: 30px;
  border: #6C227E !important;
  color: antiquewhite;
}
 .subscribe-mail .sf-input input:focus {
  border:1px solid #6C227E;
  outline: none;
  box-shadow:0px 0px 3px #6C227E;
  border-radius: 0;
}
.subscribe-mail .sf-input__bar{
  background: #6C227E;
  color: #6C227E;
  --icon-colo:#6C227E;
}
  .subscribe-btn .sf-button{
  background-color: #6C227E;
  height: 30px;
  margin-top: 3.5rem;
}
</style>
