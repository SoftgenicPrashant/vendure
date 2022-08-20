<template>
  <div id="category">
    <SfBreadcrumbs
      class="breadcrumbs desktop-only"
      :breadcrumbs="breadcrumbs"
    />
    <div class="navbar section">
      <div class="navbar__aside desktop-only">
        <LazyHydrate never>
          <SfHeading
            :level="3"
            :title="$t('Categories')"
            class="navbar__title"
          />
        </LazyHydrate>
      </div>

      <div class="navbar__main">
        <LazyHydrate on-interaction>
          <SfButton
            class="sf-button--text navbar__filters-button"
            :aria-label="$t('Filters')"
            @click="toggleFilterSidebar"
          >
            <SfIcon
              size="24px"
              color="dark-secondary"
              icon="filter2"
              class="navbar__filters-icon"
            />
            {{ $t('Filters') }}
          </SfButton>
        </LazyHydrate>

        <div class="navbar__sort desktop-only">
          <span class="navbar__label">{{ $t('Sort by') }}:</span>
          <LazyHydrate on-interaction>
            <SfSelect
              :value="sortBy.selected"
              :placeholder="$t('Select sorting')"
              class="navbar__select"
              @input="th.changeSorting"
            >
              <SfSelectOption
                v-for="option in sortBy.options"
                :key="option.id"
                :value="option.id"
                class="sort-by__option"
                >{{ option.value }}</SfSelectOption
              >
            </SfSelect>
          </LazyHydrate>
        </div>

        <div class="navbar__counter">
          <span class="navbar__label desktop-only"
            >{{ $t('Products found') }}:
          </span>
          <span class="desktop-only">{{ pagination.totalItems }}</span>
          <span class="navbar__label smartphone-only"
            >{{ pagination.totalItems }} {{ $t('Items') }}</span
          >
        </div>

        <div class="navbar__view">
          <span class="navbar__view-label desktop-only">{{ $t('View') }}</span>
          <SfIcon
            v-e2e="'tiles-icon'"
            class="navbar__view-icon"
            :color="isCategoryGridView ? 'black' : 'dark-secondary'"
            icon="tiles"
            size="12px"
            role="button"
            :aria-label="$t('Change to grid view')"
            :aria-pressed="isCategoryGridView"
            @click="changeToCategoryGridView"
          />
          <SfIcon
            v-e2e="'list-icon'"
            class="navbar__view-icon"
            :color="!isCategoryGridView ? 'black' : 'dark-secondary'"
            icon="list"
            size="12px"
            role="button"
            :aria-label="$t('Change to list view')"
            :aria-pressed="!isCategoryGridView"
            @click="changeToCategoryListView"
          />
        </div>
      </div>
    </div>

    <div class="main section">
      <div class="sidebar desktop-only">
        <LazyHydrate when-idle>
          <SfLoader
            :class="{ 'loading--categories': loading }"
            :loading="loading"
          >
            <SfAccordion :open="activeCategory" :show-chevron="true">
              <SfAccordionItem
                v-for="(cat, i) in categoryTree && categoryTree"
                :key="i"
                :header="cat.label"
              >
                <template>
                  <SfList class="list">
                    <SfListItem class="list__item">
                      <SfMenuItem :count="cat.count || ''" :label="cat.label">
                        <template #label>
                          <nuxt-link
                            :to="localePath(th.getCatLink(cat))"
                            :class="
                              cat.isCurrent ? 'sidebar--cat-selected' : ''
                            "
                          >
                            All
                          </nuxt-link>
                        </template>
                      </SfMenuItem>
                    </SfListItem>
                    <SfListItem
                      class="list__item"
                      v-for="(subCat, j) in cat.items"
                      :key="j"
                    >
                      <SfMenuItem
                        :count="subCat.count || ''"
                        :label="subCat.label"
                      >
                        <template #label="{ label }">
                          <nuxt-link
                            :to="localePath(th.getCatLink(subCat))"
                            :class="
                              subCat.isCurrent ? 'sidebar--cat-selected' : ''
                            "
                          >
                            {{ label }}
                          </nuxt-link>
                        </template>
                      </SfMenuItem>
                    </SfListItem>
                  </SfList>
                </template>
              </SfAccordionItem>
            </SfAccordion>
          </SfLoader>
        </LazyHydrate>
      </div>
      <SfLoader :class="{ loading }" :loading="loading">
        <div class="products" v-if="!loading">
          <transition-group
            v-if="isCategoryGridView"
            appear
            name="products__slide"
            tag="div"
            class="products__grid"
          >
            <SfProductCard
              v-e2e="'category-product-card'"
              v-for="(product, i) in products"
              :key="productGetters.getSlug(product)"
              :style="{ '--index': i }"
              :title="productGetters.getName(product)"
              :image="productGetters.getCoverImage(product)"
              imageHeight="20.25rem"
              imageWidth="100%"
              :regular-price="
                $n(productGetters.getPrice(product).regular, 'currency')
              "
              :max-rating="5"
              :score-rating="productGetters.getAverageRating(product)"
              :show-add-to-cart-button="true"
              :isInWishlist="isInWishlist({ product })"
              :isAddedToCart="isInCart({ product })"
              :link="
                localePath(
                  `/p/${productGetters.getId(product)}/${productGetters.getSlug(
                    product
                  )}`
                )
              "
              class="products__product-card"
              @click:wishlist="
                !isInWishlist({ product })
                  ? addItemToWishlist({ product })
                  : removeItemFromWishlist({ product })
              "
              @click:add-to-cart="addItemToCart({ product, quantity: 1 })"
            />
          </transition-group>
          <transition-group
            v-else
            appear
            name="products__slide"
            tag="div"
            class="products__list"
          >
            <SfProductCardHorizontal
              v-e2e="'category-product-card'"
              v-for="(product, i) in products"
              :key="productGetters.getSlug(product)"
              :qty="itemQuantity"
              :style="{ '--index': i }"
              :title="productGetters.getName(product)"
              :description="productGetters.getDescription(product)"
              :image="productGetters.getCoverImage(product)"
              :regular-price="
                $n(productGetters.getPrice(product).regular, 'currency')
              "
              :max-rating="5"
              :score-rating="3"
              :isInWishlist="isInWishlist({ product })"
              class="products__product-card-horizontal"
              @input="productQuantity[product._id] = $event"
              @click:wishlist="
                !isInWishlist({ product })
                  ? addItemToWishlist({ product })
                  : removeItemFromWishlist({ product })
              "
              @click:add-to-cart="
                addItemToCart({
                  product,
                  quantity:
                    Number(productQuantity[product._id]) || itemQuantity,
                })
              "
              :link="
                localePath(
                  `/p/${productGetters.getId(product)}/${productGetters.getSlug(
                    product
                  )}`
                )
              "
            >
              <template #configuration>
                <SfProperty
                  class="desktop-only"
                  name="Size"
                  value="XS"
                  style="margin: 0 0 1rem 0"
                />
                <SfProperty class="desktop-only" name="Color" value="white" />
              </template>
              <template #actions>
                <SfButton
                  v-if="!isInWishlist({ product })"
                  class="sf-button--text desktop-only"
                  style="margin: 0 0 1rem auto; display: block"
                  @click="addItemToWishlist({ product })"
                >
                  {{ $t('Save for later') }}
                </SfButton>
                <SfButton
                  v-else
                  class="sf-button--text desktop-only"
                  style="margin: 0 0 1rem auto; display: block"
                  @click="removeItemFromWishlist({ product })"
                >
                  {{ $t('Remove from wishlist') }}
                </SfButton>
              </template>
            </SfProductCardHorizontal>
          </transition-group>

          <LazyHydrate on-interaction>
            <SfPagination
              v-if="!loading"
              class="products__pagination desktop-only"
              v-show="pagination.totalPages > 1"
              :current="pagination.currentPage"
              :total="pagination.totalPages"
              :visible="5"
            />
          </LazyHydrate>

          <div
            v-show="pagination.totalPages > 1"
            class="products__show-on-page"
          >
            <span class="products_show-on-page_label">{{
              $t('Show on page')
            }}</span>
            <LazyHydrate on-interaction>
              <SfSelect
                :value="pagination.itemsPerPage.toString()"
                class="products__items-per-page"
                @input="th.changeItemsPerPage"
              >
                <SfSelectOption
                  v-for="option in pagination.pageOptions"
                  :key="option"
                  :value="option"
                  class="products_items-per-page_option"
                >
                  {{ option }}
                </SfSelectOption>
              </SfSelect>
            </LazyHydrate>
          </div>
        </div>
      </SfLoader>
    </div>

    <LazyHydrate when-idle>
      <SfSidebar
        :visible="isFilterSidebarOpen"
        :title="$t('Filters')"
        class="sidebar-filters"
        @close="toggleFilterSidebar"
      >
        <div class="filters desktop-only">
          <div v-for="(facet, i) in facets" :key="i">
            <SfHeading
              :level="4"
              :title="facet.label"
              class="filters__title sf-heading--left"
              :key="`filter-title-${facet.id}`"
            />
            <div>
              <SfFilter
                v-for="option in facet.options"
                :key="`${facet.attrName}-${option.value}`"
                :label="
                  option.attrName +
                  `${option.count ? ` (${option.count})` : ''}`
                "
                :selected="isFilterSelected(facet, option)"
                class="filters__item"
                @change="() => selectFilter(facet, option)"
              />
            </div>
          </div>
        </div>
        <SfAccordion class="filters smartphone-only">
          <div v-for="(facet, i) in facets" :key="i">
            <SfAccordionItem
              :key="`filter-title-${facet.id}`"
              :header="facet.label"
              class="filters__accordion-item"
            >
              <SfFilter
                v-for="option in facet.options"
                :key="`${facet.id}-${option.id}`"
                :label="option.attrName"
                :selected="isFilterSelected(facet, option)"
                class="filters__item"
                @change="() => selectFilter(facet, option)"
              />
            </SfAccordionItem>
          </div>
        </SfAccordion>
        <template #content-bottom>
          <div class="filters__buttons">
            <SfButton class="sf-button--full-width" @click="applyFilters" style="background: #6c227e">{{
              $t('Done')
            }}</SfButton>
            <SfButton
              class="sf-button--full-width filters__button-clear"
              @click="clearFilters"
              >{{ $t('Clear all') }}</SfButton
            >
          </div>
        </template>
      </SfSidebar>
    </LazyHydrate>
  </div>
</template>

<script>
import {
  SfSidebar,
  SfButton,
  SfList,
  SfIcon,
  SfHeading,
  SfMenuItem,
  SfFilter,
  SfProductCard,
  SfProductCardHorizontal,
  SfPagination,
  SfAccordion,
  SfSelect,
  SfBreadcrumbs,
  SfLoader,
  SfColor,
  SfProperty,
} from '@storefront-ui/vue';
import { ref, computed, onMounted } from '@vue/composition-api';
import {
  useCart,
  useWishlist,
  productGetters,
  useFacet,
  facetGetters,
} from '@vue-storefront/vendure';
import { useUiHelpers, useUiState } from '~/composables';
import { getTreeWithoutEmptyCategories } from '~/helpers';
import { onSSR } from '@vue-storefront/core';
import LazyHydrate from 'vue-lazy-hydration';
import Vue from 'vue';
// TODO(addToCart qty, horizontal): https://github.com/vuestorefront/storefront-ui/issues/1606
export default {
  name: 'Category',
  transition: 'fade',
  setup(props, context) {
    const productQuantity = ref({});
    const itemQuantity = ref(1);
    const th = useUiHelpers();
    const uiState = useUiState();
    const { addItem: addItemToCart, isInCart, cart } = useCart();
    const {
      addItem: addItemToWishlist,
      isInWishlist,
      removeItem: removeItemFromWishlist,
    } = useWishlist();
    const { result, search, loading } = useFacet();
    const { changeFilters, isFacetColor } = useUiHelpers();
    const { toggleFilterSidebar } = useUiState();
    // TODO: Refactor this to work on path rather than slugs because slug params are undefined so we have to filter them.
    const lastSlug = th.getLastSlugFromParams();
    const searchResult = computed(() =>
      facetGetters.getAgnosticSearchResult(result.value)
    );
    const sortBy = computed(() =>
      facetGetters.getSortOptions(searchResult.value)
    );
    const facets = computed(() => facetGetters.getGrouped(searchResult.value));
    const products = computed(() =>
      facetGetters.getProducts(searchResult.value)
    );
    const rawBreadcrumbs = computed(() =>
      facetGetters.getBreadcrumbsFromSlug(searchResult.value, lastSlug)
    );
    const breadcrumbs = computed(() =>
      th.getFormattedBreadcrumbs(rawBreadcrumbs.value)
    );
    // TODO: Refactor this getter
    const rawPagination = computed(() =>
      facetGetters.getPagination(searchResult.value)
    );
    const pagination = computed(() => ({
      page: parseInt(context.root.$route.query.page, 10) || 1,
      ...rawPagination.value,
    }));
    // TODO: Refactor this getter
    const rawCategoryTree = computed(() =>
      searchResult.value?.data?.categories?.map((category) => {
        const tree = facetGetters.getTree(category.collection);
        tree.isCurrent = th.doesUrlIncludesCategory(tree.slug);
        return tree;
      })
    );
    const categoryTree = computed(() =>
      getTreeWithoutEmptyCategories(rawCategoryTree.value)
    );
    const activeCategory = computed(() => {
      const items = categoryTree.value;
      if (!items || !items.length) {
        return '';
      }
      const category = items.find(
        ({ isCurrent, items }) =>
          isCurrent || items.find(({ isCurrent }) => isCurrent)
      );
      return category?.label || items[0].label;
    });
    const selectedFilters = ref({});
    const setSelectedFilters = () => {
      if (!facets.value.length || Object.keys(selectedFilters.value).length)
        return;
      selectedFilters.value = facets.value.reduce(
        (prev, curr) => ({
          ...prev,
          [curr.id]: curr.options.filter((o) => o.selected).map((o) => o.id),
        }),
        {}
      );
    };
    onSSR(async () => {
      await search({ ...th.getFacetsFromURL() });
      setSelectedFilters();
    });
    onMounted(() => {
      context.root.$scrollTo(context.root.$el, 2000);
      setSelectedFilters();
    });
    const isFilterSelected = (facet, option) =>
      (selectedFilters.value.attributes || []).includes(option.id);
    const selectFilter = (facet, option) => {
      if (!selectedFilters.value.attributes) {
        Vue.set(selectedFilters.value, 'attributes', []);
      }
      if (selectedFilters.value?.attributes.find((f) => f === option.id)) {
        const filterIndex = selectedFilters.value?.attributes.indexOf(
          option.id
        );
        if (filterIndex > -1) {
          selectedFilters.value?.attributes?.splice(filterIndex, 1);
        }
        return;
      }
      selectedFilters.value.attributes.push(option.id);
    };
    const clearFilters = () => {
      toggleFilterSidebar();
      selectedFilters.value = {};
      changeFilters(selectedFilters.value);
    };
    const applyFilters = () => {
      toggleFilterSidebar();
      changeFilters(selectedFilters.value);
    };
    return {
      ...uiState,
      productQuantity,
      th,
      products,
      categoryTree,
      loading,
      productGetters,
      pagination,
      activeCategory,
      sortBy,
      facets,
      breadcrumbs,
      addItemToWishlist,
      removeItemFromWishlist,
      isInWishlist,
      addItemToCart,
      isInCart,
      isFacetColor,
      selectFilter,
      isFilterSelected,
      selectedFilters,
      clearFilters,
      applyFilters,
      cart,
      itemQuantity,
    };
  },
  components: {
    SfButton,
    SfSidebar,
    SfIcon,
    SfList,
    SfFilter,
    SfProductCard,
    SfProductCardHorizontal,
    SfPagination,
    SfMenuItem,
    SfAccordion,
    SfSelect,
    SfBreadcrumbs,
    SfLoader,
    SfColor,
    SfHeading,
    SfProperty,
    LazyHydrate,
  },
};
</script>
<style lang="scss" scoped>
@import '~@storefront-ui/vue/styles';
#category {
  box-sizing: border-box;
  @include for-desktop {
    max-width: 1240px;
    margin: 0 auto;
  }
}
.main {
  &.section {
    padding: var(--spacer-xs);
    @include for-desktop {
      padding: 0;
    }
  }
}
.breadcrumbs {
  padding: var(--spacer-base) var(--spacer-base) var(--spacer-base)
    var(--spacer-sm);
}
.navbar {
  position: relative;
  display: flex;
  border: 1px solid var(--c-light);
  border-width: 0 0 1px 0;
  @include for-desktop {
    border-width: 1px 0 1px 0;
  }
  &.section {
    padding: var(--spacer-sm);
    @include for-desktop {
      padding: 0;
    }
  }
  &__aside,
  &__main {
    display: flex;
    align-items: center;
    padding: var(--spacer-sm) 0;
  }
  &__aside {
    flex: 0 0 15%;
    padding: var(--spacer-sm) var(--spacer-sm);
    border: 1px solid var(--c-light);
    border-width: 0 1px 0 0;
  }
  &__main {
    flex: 1;
    display: flex;
    padding: 0;
    @include for-desktop {
      padding: var(--spacer-xs) var(--spacer-xl);
    }
  }
  &__title {
    --heading-title-font-weight: var(--font-weight--semibold);
    --heading-title-font-size: var(--h3-font-size);
  }
  &__filters-icon {
    margin: 0 0 0 var(--spacer-2xs);
    order: 1;
    @include for-desktop {
      margin: 0 var(--spacer-xs) 0 0;
      order: 0;
    }
  }
  &__filters-button {
    display: flex;
    align-items: center;
    --button-font-size: var(--font-size--base);
    --button-text-decoration: none;
    --button-color: var(--c-link);
    --button-font-weight: var(--font-weight--normal);
    @include for-mobile {
      --button-font-weight: var(--font-weight--medium);
      margin-right: var(--spacer-sm);
      order: 1;
    }
    svg {
      fill: var(--c-text-muted);
      transition: fill 150ms ease;
    }
    &:hover {
      svg {
        fill: var(--c-primary);
      }
    }
  }
  &__label {
    font-family: var(--font-family--secondary);
    font-weight: var(--font-weight--normal);
    color: var(--c-text-muted);
    @include for-desktop {
      margin: 0 var(--spacer-2xs) 0 0;
    }
  }
  &__select {
    --component-select-width: 220px;
    --component-select-padding: 0;
    --component-select-selected-padding: 0 var(--spacer-lg) 0 var(--spacer-2xs);
    --component-select-margin: 0;
    --component-select-error-message-height: 0;
  }
  &__sort {
    display: flex;
    align-items: center;
    margin: 0 auto 0 var(--spacer-2xl);
  }
  &__counter {
    font-family: var(--font-family--secondary);
    margin: auto;
    @include for-desktop {
      margin: auto 0 auto auto;
    }
  }
  &__view {
    display: flex;
    order: -1;
    align-items: center;
    margin: 0;
    @include for-desktop {
      margin: 0 0 0 var(--spacer-2xl);
      order: 0;
    }
    &-icon {
      cursor: pointer;
      margin: 0 var(--spacer-base) 0 var(--spacer-sm);
      @include for-desktop {
        margin: 0 var(--spacer-base) 0 0;
      }
      &:last-child {
        margin: 0;
      }
    }
    &-button {
      cursor: pointer;
      margin: 0 var(--spacer-base) 0 var(--spacer-sm);
      @include for-desktop {
        margin: 0 var(--spacer-base) 0 0;
      }
      &:last-child {
        margin: 0;
      }
    }
    &-label {
      margin: 0 var(--spacer-sm) 0 0;
      font: var(--font-weight--normal) var(--font-size--base) / 1.6
        var(--font-family--secondary);
      text-decoration: none;
      color: var(--c-link);
    }
  }
}
.sort-by {
  --component-select-dropdown-z-index: 1;
  flex: unset;
  width: 11.875rem;
}
.main {
  display: flex;
}
.sidebar {
  flex: 0 0 15%;
  padding: var(--spacer-sm);
  border: 1px solid var(--c-light);
  border-width: 0 1px 0 0;
}
.sidebar {
  @include for-desktop {
    ::v-deep .sf-accordion-item__header.is-open {
      --accordion-item-header-border-width: 0;
      --accordion-item-header-color: #6c227e !important;
      --chevron-color: #6c227e !important;
      font-style: normal;
      font-weight: 500;
      font-size: 18px;
      line-height: 24px;
    }
    ::v-deep .sf-accordion-item__header {
      font-style: normal;
      font-weight: 500;
      font-size: 18px;
      line-height: 24px;
    }
    ::v-deep .sf-menu-item__label {
      font-style: normal;
      font-weight: 500;
      font-size: 16px;
      line-height: 24px;
    }
  }
}
.sidebar-filters {
  --sidebar-title-display: none;
  --sidebar-top-padding: 0;
  @include for-desktop {
    --sidebar-content-padding: 0 var(--spacer-xl);
    --sidebar-bottom-padding: 0 var(--spacer-xl);
  }
}

.list {
  --menu-item-font-size: var(--font-size--sm);
  &__item {
    &:not(:last-of-type) {
      --list-item-margin: 0 0 var(--spacer-sm) 0;
    }
  }
}
.products {
  box-sizing: border-box;
  flex: 1;
  margin: 0;
  &__grid,
  &__list {
    display: flex;
    flex-wrap: wrap;
  }
  &__grid {
    justify-content: center;
    @include for-desktop {
      justify-content: space-between;
    }
  }
  &__product-card {
    background: rgba(255, 255, 255, 0.15);
    border: 1px solid rgba(137, 94, 206, 0.3);
    box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.07);
    border-radius: 8px;
    padding-top: 0;
    --product-card-max-width: 6rem;
    --product-card-title-margin: var(--spacer-2xs) 0 0 0;
    --price-regular-font-line-height: 1;
    margin-bottom: var(--spacer-sm);
    ::v-deep .sf-product-card__price {
      margin: var(--spacer-2xs) 0 var(--spacer-xs);
    }
    @include for-desktop {
      ::v-deep .sf-product-card__title {
        font-style: normal;
        font-weight: 600;
        font-size: 12px;
        line-height: 24px;
        background-color: rgb(230, 246, 252);
      }
    }

    flex: 1 1 50%;
    @include for-desktop {
      margin-bottom: 10px;

      height: 135px;
      --product-card-max-width: 24.5%;
      --product-card-title-margin: var(--spacer-base) 0 0 0;

      ::v-deep .sf-image--placeholder {
        display: none !important;
      }
      ::v-deep .sf-image {
        display: none;
      }
      ::v-deep .sf-product-card__add-button {
        top: 4rem !important;
        z-index: 1;
        background: #6c5b71;
      }
      ::v-deep .sf-product-card__wishlist-icon {
        margin-top: 0;
        bottom: 6rem;
      }
    }
  }
  &__product-card-horizontal {
    margin-bottom: var(--spacer-sm);
    flex: 0 0 100%;
    ::v-deep .sf-product-card-horizontal__wishlist-icon {
      .sf-icon {
        width: 1.5rem;
        height: 1.5rem;
      }
    }
  }
  &__slide-enter {
    opacity: 0;
    transform: scale(0.5);
  }
  &__slide-enter-active {
    transition: all 0.2s ease;
    transition-delay: calc(0.1s * var(--index));
  }
  &__pagination {
    display: flex;
    justify-content: center;
    margin: var(--spacer-base) 0;
  }
  @include for-desktop {
    ::v-deep .sf-image--placeholder {
      display: none;
    }
    ::v-deep .sf-image {
      display: none;
    }
    ::v-deep .sf-add-to-cart__button {
      background-color: #6c227e;
    }
    margin: var(--spacer-sm) 0 0 var(--spacer-sm);
    &__pagination {
      justify-content: flex-start;
      margin: var(--spacer-xl) 0 0 0;
    }
    &__product-card-horizontal {
      margin: var(--spacer-lg) 0;
    }
    &__product-card {
      flex: 1 1 25%;
    }
    &__list {
      margin: 0 0 0 var(--spacer-sm);
    }
  }
  &__show-on-page {
    display: flex;
    justify-content: flex-end;
    align-items: baseline;
    &__label {
      font-family: var(--font-family--secondary);
      font-size: var(--font-size--sm);
    }
  }
}
.filters {
  &__title {
    --heading-title-font-size: var(--font-size--xl);
    margin: var(--spacer-xl) 0 var(--spacer-base) 0;
    &:first-child {
      margin: calc(var(--spacer-xl) + var(--spacer-base)) 0 var(--spacer-xs) 0;
    }
  }
  &__colors {
    display: flex;
  }
  &__color {
    margin: var(--spacer-xs) var(--spacer-xs) var(--spacer-xs) 0;
  }
  &__chosen {
    color: var(--c-text-muted);
    font-weight: var(--font-weight--normal);
    font-family: var(--font-family--secondary);
    position: absolute;
    right: var(--spacer-xl);
  }
  &__item {
    --radio-container-padding: 0 var(--spacer-sm) 0 var(--spacer-xl);
    --radio-background: transparent;
    --filter-label-color: var(--c-secondary-variant);
    --filter-count-color: var(--c-secondary-variant);
    --checkbox-padding: 0 var(--spacer-sm) 0 var(--spacer-xl);
    padding: var(--spacer-sm) 0;
    border-bottom: 1px solid var(--c-light);
    &:last-child {
      border-bottom: 0;
    }
    @include for-desktop {
      --checkbox-padding: 0;
      margin: var(--spacer-sm) 0;
      border: 0;
      padding: 0;
    }
  }

  &__accordion-item {
    --accordion-item-content-padding: 0;
    position: relative;
    left: 50%;
    right: 50%;
    margin-left: -50vw;
    margin-right: -50vw;
    width: 100vw;
  }
  &__buttons {
    margin: var(--spacer-sm) 0;
  }
  &__button-clear {
    --button-background: var(--c-light);
    --button-color: var(--c-dark-variant);
    margin: var(--spacer-xs) 0 0 0;
  }
}
::v-deep .sf-sidebar__circle-icon {
  background-color: #6c227e;
}
::v-deep .done {
  background-color: #6c227e;
}
</style>