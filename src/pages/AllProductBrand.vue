<template>
	<q-layout view="lHh Lpr lFf">
		<q-header class="mobile-layout-on-desktop">
			<q-toolbar class="bg-distrodakwah text-white">
				<q-btn flat round dense to="/">
					<q-icon name="arrow_back" color="white" />
				</q-btn>

				<q-toolbar-title>
					<span style="font-size: 16px; font-weight: bold">{{
						categoryName
					}}</span>
				</q-toolbar-title>
			</q-toolbar>
		</q-header>

		<!-- <q-footer class="mobile-layout-on-desktop">
      <center>
        filter disini
      </center>
    </q-footer>-->

		<q-page-container class="mobile-layout-on-desktop">
			<q-page>
				<div class="bg-grey-3" style="height: 100%">
					<div style="background-color: white;">
						<!-- <div v-infinite-scroll="getProduct" infinite-scroll-disabled="busy" infinite-scroll-distance="limit" class="row q-px-md" style="padding: 15px 10px"> -->
						<!-- <div class="product-card"> -->
						<!-- <div
                class="row q-px-md"
                style="margin-bottom: 15px padding: 15px 10px"
                v-for="(product, index) in newProduct"
                :key="index"
                v-if="product.category_id === Number(categoryID)"
            >-->
						<div
							class="row q-px-md q-py-lg"
							style="margin-bottom: 15px padding: 15px 10px"
						>
							<div
								class="col-6"
								style="margin-bottom: 15px"
								v-for="(product, index) in newProduct"
								:key="index"
							>
								<KeepProductCard
									:product="product"
									:user="user"
									v-if="product.brand_id === 7"
								/>
								<VendorProductCard :product="product" :user="user" v-else />
							</div>
						</div>
						<!-- </div> -->
						<!-- infiniteScroll -->
						<!-- </div> -->

						<!-- <div class="row q-px-md" style="padding: 15px 10px">
              <div class="col-6" style="margin-bottom: 15px" v-for="(product, index) in newProduct" :key="index">

                <q-card class="my-card bg-grey-2" style="margin: 0 5px" flat bordered>
                  <transition
                    appear
                    enter-active-class="animated fadeIn"
                    leave-active-class="animated fadeOut"
                  >
                    <img :src="product.featured_image" style="width: 100%" v-show="featuredImageShow == true">
                  </transition>

                  <center>
                    <q-spinner
                      color="dark"
                      size="2em"
                      v-show="innerLoading == true"
                      style="margin: 10px 0"
                    />
                  </center>

                  <q-card-section style="padding: 10px 16px 16px 16px">
                    <center>
                      <div style="font-family: 'Open Sans';font-size: 12px; font-weight: bold; margin-bottom: 5px">{{product.product_name}}</div>
                      <div class="text-black" style="font-size: 10px;">Keuntungan Anda :</div>
                      <div class="q-px-sm q-py-xs bg-green">
                        <div class="text-white" style="font-weight: bolder; margin-top:0" v-if="user.role.id === 9">{{'Rp' + formatPrice(product.price * product.reseller_exclusive_price / 100)}}</div>
                        <div class="text-white" style="font-weight: bolder; margin-top:0" v-else-if="user.role.id === 8">{{'Rp' + formatPrice(product.price * product.reseller_pro_price / 100)}}</div>
                      </div>
                    </center>
                  </q-card-section>

                  <q-card-section>
                    <center>
                      <q-btn :to="'/detail/' + product.id" flat class="bg-orange-8 text-white" style="padding-top: 0px; padding-bottom: 0px"><span style="text-transform: capitalize;">Beli Sekarang</span></q-btn>
                    </center>
                  </q-card-section>

                </q-card>

              </div>
            </div>-->
						<div class="q-pb-lg flex flex-center">
							<q-pagination
								flat
								v-model="page"
								color="orange-8"
								:max="lastPage"
								:max-pages="6"
								:direction-links="true"
								@input="onChangePage"
							></q-pagination>
						</div>
					</div>
				</div>
				<!-- <div class="pagination-container middle">
          <div class="pagination">
                <ul>
                    <li><a href="#" v-html="'<'"></a></li>
                    <li v-for="n in lastPage">
                          <a href="#" :class="'active' : n == page">{{n}}</a>

                    </li>
                    <li><a href="#" v-html="'>'"></a></li>
                </ul>
          </div>
        </div>-->
			</q-page>
		</q-page-container>
	</q-layout>
</template>

<script>
import axios from "axios";
import { apiDomain, getProductByCategoryUrl, getHeader } from "src/config";
// Loading
import { QSpinnerPuff, QSpinnerDots } from "quasar";
import infiniteScroll from "vue-infinite-scroll";
import Vue from "vue";
//components
import VendorProductCard from "../components/vendorProductCard.vue";
import KeepProductCard from "../components/keepProductCard.vue";

Vue.use(infiniteScroll);

export default {
	name: "ProductsByCategory",
	components: {
		VendorProductCard,
		KeepProductCard
	},
	data() {
		return {
			// Product Section
			dataProduct: [],
			sortField: "id",
			sortOrder: "desc",
			defaultSortOrder: "desc",
			page: 1,
			perPage: 4,
			isPaginated: true,
			// Loading
			innerLoading: false,
			featuredImageShow: true,
			// user
			user: [],
			categoryName: "",
			categoryID: null,
			// limit: 2,
			productsCount: 0,
			busy: false,
			lastPage: 0
		};
	},
	computed: {
		newProduct() {
			return this.dataProduct.slice().reverse();
		}
	},
	created() {
		this.user = JSON.parse(window.localStorage.getItem("profileUser"));
		this.categoryName = this.$route.params.category;
		this.categoryID = this.$route.params.category_id;
		this.$q.loading.show({
			spinner: QSpinnerPuff,
			spinnerColor: "black",
			spinnerSize: 50,
			backgroundColor: "grey",
			message: "<b>Mohon Tunggu..</b>",
			messageColor: "black"
		});
	},
	mounted() {
		this.$q.loading.hide();

		this.getProduct();
	},
	methods: {
		onChangePage(page) {
			this.getProduct();
		},
		getProduct() {
			// if (this.productsCount !== this.dataProduct.length || this.productsCount === 0) {
			if (this.productsCount === 0) {
				const params = [
					//'api_key=bb6f51bef07465653c3e553d6ab161a8',
					`sort=${this.sortField}`,
					`order=${this.sortOrder}`,
					`limit=${this.perPage}`,
					`page=${this.page}`
				].join("&");

				this.innerLoading = true;
				this.featuredImageShow = false;
				this.busy = true;

				axios
					.get(getProductByCategoryUrl + "/" + this.categoryID + `?${params}`, {
						headers: getHeader()
					})
					.then(response => {
						const data = response.data.data;
						if (response.status === 200) {
							// const append = response.data.data.slice(
							// const append = data.data.slice(
							//   this.dataProduct.length,
							//   this.dataProduct.length + this.perPage
							// );
							// this.productsCount = response.data.data.length
							// this.productsCount = data.data.length  ;
							this.dataProduct = data.data;
							this.lastPage = data.last_page;
							this.page = data.current_page;
							setTimeout(() => {
								this.$q.loading.hide();
								this.busy = false;
								this.innerLoading = false;
								this.featuredImageShow = true;
							}, 100);
						}
					})
					.catch(error => {
						if (error.response) {
							console.log(error.response);
						}
					});
			}
		},
		formatPrice(value) {
			let val = (value / 1).toFixed(0).replace(".", ",");
			return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".");
		}
	}
};
</script>

<style>
.promo-text {
	font-family: "Open Sans";
	font-size: 13px;
	font-weight: bold;
	margin: 0;
	padding-left: 12px;
}
.link-text {
	font-family: "Open Sans";
	font-size: 13px;
	margin: 0;
	padding-right: 10px;
}
.swiper-slide {
	width: 85%;
}
.category-title {
	font-size: 13px;
	margin: 0;
	text-align: center;
	text-transform: capitalize;
	line-height: 15px;
	font-weight: 400;
	padding: 10px 0px;
}

.pagination-container {
	margin: 0 10px;
}

</style>
