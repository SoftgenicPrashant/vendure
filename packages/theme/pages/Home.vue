<template>
  <div id="home">
    <LazyHydrate when-idle>
      <SfHero class="hero">
        <SfHeroItem
          v-for="(hero, i) in heroes"
          :key="i"
          :title="hero.title"
          :subtitle="hero.subtitle"
          buttonText="Shop Now"
          :background="hero.background"
          :image="hero.image"
          :class="hero.className"
         
        />
      </SfHero>
    </LazyHydrate>
<div class="catcard">
<template>
  <SfCategoryCard
    label="Personal Care"
    count="32"
    :background='{"mobile":"/homepage/tabs.jpg","desktop":"/homepage/tabs.jpg"}'
    link="#"
  />
    <SfCategoryCard
    label="Personal Care"
    count="32"
    :background='{"mobile":"/homepage/tabs.jpg","desktop":"/homepage/tabs.jpg"}'
    link="#"
  />
     <SfCategoryCard
    label="Personal Care"
    count="32"
    :background='{"mobile":"/homepage/tabs.jpg","desktop":"/homepage/tabs.jpg"}'
    link="#"
  />
     <SfCategoryCard
    label="Personal Care"
    count="32"
    :background='{"mobile":"/homepage/tabs.jpg","desktop":"/homepage/tabs.jpg"}'
    link="#"
  />
</template>
</div>
    <LazyHydrate when-visible>
      <SfBannerGrid :banner-grid="1" class="banner-grid">
        <template v-for="item in banners" v-slot:[item.slot]>
          <SfBanner
            :key="item.slot"
            :title="item.title"
            :subtitle="item.subtitle"
            :description="item.description"
            :button-text="item.buttonText"
            :link="localePath(item.link)"
            :image="item.image"
            :class="item.class"
            
          />
        </template>
      </SfBannerGrid>
    </LazyHydrate>

    <LazyHydrate when-visible>
      <div class="similar-products">
        <SfHeading title="Related Products" :level="2"/>
        <nuxt-link :to="localePath('/c/women')" class="smartphone-only">See all</nuxt-link>
      </div>
    </LazyHydrate>

    <LazyHydrate when-visible>
        <SfCarousel class="carousel" :settings="{ peek: 16, breakpoints: { 1023: { peek: 0, perView: 2 } } }">
          <template #prev="{go}">
            <SfArrow
              aria-label="prev"
              class="sf-arrow--left sf-arrow--long"
              @click="go('prev')"
            />
          </template>
          <template #next="{go}">
            <SfArrow
              aria-label="next"
              class="sf-arrow--right sf-arrow--long"
              @click="go('next')"
            />
          </template>
          <SfCarouselItem class="carousel__item" v-for="(product, i) in products" :key="i">
            <SfProductCard
              :title="product.productName"
               image="/assets/storybook/Home/productB.jpg"
              :regular-price="$n(getCalculatedPrice(product.price.value), 'currency')"
              :link="localePath(`/p/${product.productId}/${product.slug}`)"
              class="carousel__item__product"

            />
          </SfCarouselItem>
        </SfCarousel>
    </LazyHydrate>

    <LazyHydrate when-visible>
      <SfCallToAction
        title="Subscribe to Newsletters"
        description="Be aware of upcoming sales and events. Receive gifts and special offers!"
        image="/homepage/newsletter.webp"
        class="call-to-action"
        button-text="subscribe"
      />
      
    </LazyHydrate>

    <LazyHydrate when-visible>
      <InstagramFeed />
    </LazyHydrate>
    <div>
      <MobileStoreBannerVue />
    </div>
  </div>
</template>
<script>
import {
  SfHero,
  SfBanner,
  SfCallToAction,
  SfSection,
  SfCarousel,
  SfProductCard,
  SfImage,
  SfBannerGrid,
  SfHeading,
  SfArrow,
  SfButton,
  SfCategoryCard
} from '@storefront-ui/vue';
import InstagramFeed from '~/components/InstagramFeed.vue';
import MobileStoreBannerVue from '~/components/MobileStoreBanner.vue';
import LazyHydrate from 'vue-lazy-hydration';
import cacheControl from './../helpers/cacheControl';
import { useContext, computed } from '@nuxtjs/composition-api';
import { useFacet } from '@vue-storefront/vendure';
import { onSSR } from '@vue-storefront/core';
import { getCalculatedPrice } from '~/helpers';

export default {
  name: 'Home',
  middleware: cacheControl({
    'max-age': 60,
    'stale-when-revalidate': 5
  }),
  components: {
    InstagramFeed,
    SfHero,
    SfBanner,
    SfCallToAction,
    SfSection,
    SfCarousel,
    SfProductCard,
    SfImage,
    SfBannerGrid,
    SfHeading,
    SfArrow,
    SfButton,
    LazyHydrate,
    SfCategoryCard,
    MobileStoreBannerVue
  },
  setup() {

    const { $config } = useContext();
    const heroes = [
      {
        title: 'For all of your pharmaceutical needs!',
        subtitle: 'National Apothecary Solutions',
        
        background: '#eceff1',
        image: '/homepage/photo.png'
      },
            {
        title: 'For all of your pharmaceutical needs!',
        subtitle: 'National Apothecary Solutions',
        background: '#eceff1',
        image: '/homepage/photo.png'
      },
      // {
      //   title: 'Colorful summer dresses are already in store',
      //   subtitle: 'SUMMER COLLECTION 2019',
      //   background: '#efebe9',
      //   image: '/homepage/bannerA.webp',
      //   className:
      //       'sf-hero-item--position-bg-top-left sf-hero-item--align-right'
      // },
      {
        title: 'ACETAZOLAMIDE 125 MG TAB 100',
        subtitle: 'EYWA PHARMA INC',
        background: '#fce4ec',
        image: '/homepage/hero1.jpg'
      }
    ];
    const banners = [
      {
        slot: 'banner-A',
        subtitle: 'SHOES',
        title: 'PRODUCT NAME',
        description:
            'This is an example of a banner for promoting a special product',
        buttonText: 'Shop now',
        image: {
          mobile: '/homepage/pills1.png',
          desktop: '/homepage/pills1.png'
        },
        class: 'sf-banner--slim banner__start desktop-only',
        link: $config.theme.home.bannerA.link
      },
      {
        slot: 'banner-B',
        subtitle: 'CATEGORY 1',
        title: 'PRODUCT NAME',
        description:
            'This is an example of a banner for promoting a special product',
        buttonText: 'Shop now',
        image: '/homepage/pills.png',
        class: 'sf-banner--slim banner-central desktop-only',
        link: $config.theme.home.bannerB.link
      },
      {
        slot: 'banner-C',
        subtitle: 'ACCU-CHECK AVIVA PLUS ',
        title: 'Test Strips',
        image: 'https://media.istockphoto.com/photos/urine-test-strips-picture-id1074268776?k=20&m=1074268776&s=170667a&w=0&h=STI2HogMOG3zu9sjAb3Tl5F_5NsuJADqtGc338dfFtU=',
        class: 'sf-banner--slim banner__tshirt',
        link: $config.theme.home.bannerC.link
      },
      {
        slot: 'banner-D',
        subtitle: 'ALL BRANDS',
        title: 'ABACAVIR',
        image: '/homepage/b1.jpeg',
        class: 'sf-banner--slim',
        link: $config.theme.home.bannerD.link
      }
    ];
    const { search, result } = useFacet();

    onSSR(async () => {
      await search({ sort: { name: 'DESC' }, take: 8});
    });

    const products = computed(() => result.value.data.items);

    return {
      heroes,
      banners,
      products,
      getCalculatedPrice
    };

  }
};
</script>

<style lang="scss" scoped>
#layout{
  @include for-desktop{
    padding: 0 !important;
    width: 100%;
  }
}
#home {
  box-sizing: border-box;
  padding: 0 var(--spacer-sm);
  @include for-desktop {
    max-width: 1240px;
    
    padding: 0;
    margin: 0 auto;
  }
}

.hero {
  
  margin: var(--spacer-xl) auto var(--spacer-lg);
  --hero-item-background-position: center;
  @include for-desktop {
    ::v-deep .sf-hero .hero{
      width: 100%;
    }
      ::v-deep .sf-hero-item__button .sf-button{
      background: #6C227E;
    }
    margin: var(--spacer-xl) auto var(--spacer-2xl);
    ::v-deep .sf-hero-item__subtitle{
      font-style: normal;
      font-weight: 700;
      font-size: 16px;
      line-height: 22px;
      color: #6C227E;
    }
    ::v-deep .sf-hero-item__title{
      font-style: normal;
      font-weight: 700;
      font-size: 52px;
      line-height: 48px;
      color: #F1F2F3;
      
    }
    
  ::v-deep .sf-bullets .sf-button {
    background: #6C227E;
  }
  }
  .sf-hero-item {
    &:nth-child(even) {
      --hero-item-background-position: left;
      @include for-mobile {
        --hero-item-background-position: 30%;
        ::v-deep .sf-hero-item__subtitle,
        ::v-deep .sf-hero-item__title {
          text-align: left;
          width: 100%;
          padding-left: var(--spacer-sm);
        }
      }
    }
  }
  ::v-deep .sf-hero__control {
    &--right, &--left {
      display: none;
    }
  }
}

.banner-grid {
  --banner-container-width: 50%;
  margin: var(--spacer-xl) 0;
  ::v-deep .sf-link:hover {
    color: var(--c-white);
  }

  @include for-desktop {
    margin: var(--spacer-2xl) 0;
  
    ::v-deep .sf-link {
      --button-width: auto;
      text-decoration: none;
    }

  }
  
}

.banner {
  &__start{
        ::v-deep .sf-banner__title{
        font-style: normal;
        font-weight: 700;
        font-size: 30px;
        line-height: 48px;
        display: inline;
      
    }
    @include for-desktop{
       ::v-deep .sf-button.color-secondary{
        margin-top: 15rem;
      }
        ::v-deep .sf-banner__description{
        
        font-style: normal;
      font-weight: 400;
      font-size: 16px;
      line-height: 22px;
      /* or 138% */


      /* MainTextColour/Primary */

      color: #1D1F22;

      }
        ::v-deep .sf-banner__subtitle{
        padding-top: 5rem;
        font-style: normal;
      font-weight: 700;
      font-size: 16px;
      line-height: 22px;
      /* identical to box height, or 138% */


      /* Primary Grey */

      color: #4A4444;

            }
    }

  }
  &__tshirt {
    background-position: left;
    
           ::v-deep .sf-banner__title{
        font-style: normal;
        font-weight: 700;
        font-size: 26px;
        line-height: 48px;
        display: inline;
      
    }
    
    
  }
  &-central {
    @include for-desktop {
      
      ::v-deep .sf-button.color-secondary{
        margin-top: 15rem;
      }
      ::v-deep .sf-banner__description{
      
        font-style: normal;
      font-weight: 400;
      font-size: 16px;
      line-height: 22px;
      /* or 138% */


      /* MainTextColour/Primary */

      color: #1D1F22;

      }
      ::v-deep .sf-banner__subtitle{
        padding-top: 5rem;
        font-style: normal;
      font-weight: 700;
      font-size: 16px;
      line-height: 22px;
      /* identical to box height, or 138% */


      /* Primary Grey */

      color: #4A4444;

            }
      --banner-container-flex: 0 0 70%;
        ::v-deep .sf-banner__title{
        font-style: normal;
        font-weight: 700;
        font-size: 30px;
        line-height: 48px;
        display: flex;
      
    }
    }

  }
}

.similar-products {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-bottom: var(--spacer-2xs);
  --heading-padding: 0;
  border-bottom: 1px var(--c-light) solid;
  @include for-desktop {
    border-bottom: 0;
    justify-content: center;
    padding-bottom: 0;
   
  }
}
.carousel__item__product {
background: rgba(255, 255, 255, 0.15);
border: 1px solid rgba(137, 94, 206, 0.3);
box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.07);
border-radius: 8px;
margin-top: 6rem;
}
 ::v-deep .sf-product-card__image .sf-image {
    --image-height: 1.375rem;
    --image-width: 9.375rem;
    object-fit: cover;
    // @include for-desktop {
    //   --image-width: 9.125rem;
    //   --image-height: 3.75rem;
    // }
  }

.call-to-action {
  background-position: right;
  margin: var(--spacer-xs) 0;
  @include for-desktop {
          ::v-deep .sf-button{
      background: #6C227E;
    }
    margin: var(--spacer-xl) 0 var(--spacer-2xl) 0;

  }
    @include for-mobile {
          ::v-deep .sf-button{
      background: #6C227E;
    }
  

  }
  .sf-call-to-action__button .sf-button{
    background-color: #6C227E !important;
  }
 
}


.carousel {
    margin: 1rem calc(-1 * var(--spacer-sm)) 0 0;
  @include for-desktop {
    margin: 0;
  }
  &__item {
    margin: 1.375rem 0 2.5rem 0;
    @include for-desktop {
      margin: var(--spacer-xl) 0 var(--spacer-xl) 0;
    }
    &__product {
      --product-card-add-button-transform: translate3d(0, 30%, 0);
    }
    :v-deep .sf-image--placeholder {
      display: none;
    }
  }
  ::v-deep .sf-arrow--long .sf-arrow--right {
    --arrow-icon-transform: rotate(180deg);
     -webkit-transform-origin: center;
     transform-origin: center;
  }
}
.catcard{
  @include for-desktop{
    display: flex;
    justify-content: space-evenly;
  }
  ::v-deep .sf-category-card__details{
    background-color: #6C227E;
  }
  @include for-mobile{
        justify-content: center;
        align-items: center;
        
     ::v-deep .sf-category-card__details{
    background-color: #6C227E;
  }
  }
}
</style>
