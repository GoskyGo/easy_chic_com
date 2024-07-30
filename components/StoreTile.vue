<template>
  <div class="store-info">
    <div class="btn-wrap">
      <div class="store-mark w-100">
        <!-- <p class="store-name">{{ storeName }}</p>
        <h6 class="store-date">{{$t('store.memberSince')}} <b class="block">{{storeDate}}</b></h6> -->
        <img
          class="logo-imge-custome"
              :src="imageURL({'image': site_setting.header_logo})"
              :alt="$t('footer.siteLogo')"
              height="40"
              width="139"
        >
        <div class="store-mark wrap">
          <h2 class="store-name">{{ storeName }}</h2>
          <div class="store-mark">
            <p class="store-name mr-5">{{ formatPrice(review)}}</p>
            <p class="store-date mr-5">Calificación</p>|
            <p class="store-name ml-5 mr-5">{{ total}}</p>
            <p class="store-date mr-5">Artículos</p>|
            <p class="store-name ml-5 mr-5">{{Math.floor(Math.random() * 99)}}K+</p>
            <p class="store-date ">Seguidores</p>
          </div>
         
      </div>
      </div>
      
      <div class="action-btn store-mark w-100">
        <slot
          name="followBtn"
        >
          <follow-btn 
           class="visit-btn w-50 custome-btn-follow mr-10"
          :storeId="store.id"
          />
        </slot>

        <nuxt-link
          class="visit-btn ajax-btn w-50 custome-btn-follow ml-10"
          :to="storeLink(store)"
        >
          {{$t('store.visitStore')}}
        </nuxt-link>
      </div>
    </div>

  </div>
</template>

<script>
  import moment from "moment";
  import util from "../mixin/util";
  import AjaxButton from "./AjaxButton";
  import FollowBtn from "./FollowBtn";
    import { mapGetters, mapActions} from 'vuex'

  export default {
    name: 'StoreTile',
    data() {
      return {
        ajaxing: false,
        total:null,
        review:null
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

      storeName(){
        return this.store?.name
      },

      storeDate(){
        return moment(this.store?.created_at).format('MMM DD, YYYY')
      },
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
            console.log(data.data);
        this.total=data?.data?.result.total;
        this.review=data?.data?.review;
            console.log(data?.data.review);
      },
      ...mapGetters('common', ['site_setting', 'setting', 'topBanner', 'headerLinks']),
 
      ...mapGetters('language', ['langCode']),
    },
    
    methods: {
    },
    async mounted() {
      console.log(this.storeData.total);

    },
  }
</script>

<style>

</style>
