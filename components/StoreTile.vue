<template>
  <div class="store-info p-0">
    <h3 class="about-store">Sobre La Tienda</h3>
    <div class="btn-wrap">
      <div class="store-mark w-100">
        <!-- <p class="store-name">{{ storeName }}</p>
        <h6 class="store-date">{{$t('store.memberSince')}} <b class="block">{{storeDate}}</b></h6> -->
        <img class="logo-imge-custome" :src="imageURL({ 'image': site_setting.header_logo })"
          :alt="$t('footer.siteLogo')" height="40" width="139">
        <div class="store-mark wrap">
          <h2 class="store-name ">{{ storeName }}</h2>
          <div class="store-mark">

            <p class="store-date mr-5">Calificación:</p>
            <p class="store-date mr-5">{{ review ? formatPrice(review) : 0 }}</p>

            <p class="store-date mr-5"> Artículos:</p>
            <p class="store-date ml-5 mr-5">{{ total }}</p>
            <p class="store-date "> Seguidores:</p>
            <p class="store-date ml-5 mr-5">{{ follow_count < 1000 ? follow_count : Math.floor(follow_count / 1000) + "K+"
                }}</p>

          </div>

        </div>
      </div>
      <p class="shop-discription">{{ discription }}</p>
      <div class="action-btn store-mark w-100 gap-10">
        <nuxt-link class="visit-btn ajax-btn w-50 custome-btn-follow" :to="storeLink(store)">
          {{ $t('store.visitStore') }}
        </nuxt-link>

        <slot name="followBtn">
          <follow-btn class="visit-btn w-50 custome-btn-follow " :following="following" :store-id="store.id"
            @change-following="update_follow" />
        </slot>
      </div>
    </div>

  </div>
</template>

<script>
import moment from "moment";
import util from "../mixin/util";
import AjaxButton from "./AjaxButton";
import FollowBtn from "./FollowBtn";
import { mapGetters, mapActions } from 'vuex'

export default {
  name: 'StoreTile',
  data() {
    return {
      ajaxing: false,
      total: null,
      review: null,
      discription: null,
      follow_count: 0,
      following: null
    }
  },
  components: {
    FollowBtn,
    AjaxButton
  },
  props: {
    store: {
      type: Object
    },
  },
  mixins: [util],
  computed: {

    storeName() {
      this.storeData();
      return this.store?.name
    },

    storeDate() {
      return moment(this.store?.created_at).format('MMM DD, YYYY')
    },
    ...mapGetters('common', ['site_setting', 'setting', 'topBanner', 'headerLinks']),
    ...mapGetters('language', ['langCode']),
  },

  methods: {
   
    async storeData() {
      const data = await this.$store.dispatch('common/ssrGetRequest', {
        params: {
          slug: this.store.name,
          sortby: '',
          page: '',
          required_rating: true
        },
        api: 'store',
        lang: this.$store.state.language.langCode,
        requiredToken: true
      })
      this.total = data?.data?.result.total ? data?.data?.result.total : "";
      this.review = data?.data?.review;
      this.discription = data?.data.store.meta_description ? data?.data.store.meta_description : "";
      this.follow_count = data?.data.store.follow_count ? data?.data.store.follow_count : 0;
      this.following = data?.data?.following;

    },
    update_follow() {
      this.storeData();
    },
  },
}
</script>

<style></style>
