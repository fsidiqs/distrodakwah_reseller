<template>
	<q-layout view="hHh lpR fFf">
		<q-header class="mobile-layout-on-desktop">
			<q-toolbar class="bg-distrodakwah text-white">
				<q-btn flat round dense to="/cart">
					<q-icon name="arrow_back" color="white" />
				</q-btn>
				<q-toolbar-title>
					<span style="font-size: 16px; font-weight: bold"
						>Alamat Pengiriman</span
					>
				</q-toolbar-title>
			</q-toolbar>
		</q-header>

		<q-footer
			class="bg-white text-black mobile-layout-on-desktop"
			style="border-top: 2px solid #eee"
		>
			<q-toolbar class="bg-white text-black">
				<q-space />
				<q-btn
					flat
					class="bg-orange-8 text-white full-width"
					@click="reviewOrder"
					>Lihat Ringkasan Pesanan</q-btn
				>
			</q-toolbar>
		</q-footer>

		<q-page-container class="mobile-layout-on-desktop">
			<q-page class="bg-white">
				<div class="bg-grey-3" style="height: 100%">
					<div
						style="background-color: white; margin-bottom: 5px; padding: 18px 0 15px 0"
					>
						<div class="rpw q-px-lg">
							<div class="column justify-start">
								<div class="col">
									<!-- <h6 style="font-size: 12px; margin: 0 0 8px 0; font-family: 'Open Sans'; line-height: 18px">Pilih dari daftar pelanggan kamu</h6>
                  <q-checkbox keep-color dense color="orange-8" v-model="addressSelect" style="margin-bottom: 10px"><h6 style="font-size: 12px; margin: 0; font-family: 'Open Sans'; line-height: 18px; font-weight: bold">Abdullah - 0821233344455</h6><h6 style="font-size: 12px; margin: 0; font-family: 'Open Sans'; line-height: 18px">Perumahan Taman Adhidarma No B3, Jl Serbaguna, Kelurahan Klayan, Kecamatan Gunungjati, Kabupaten Cirebon</h6></q-checkbox>
                  <h6 style="font-size: 12px; margin: 0 0 8px 0; font-family: 'Open Sans'; line-height: 18px" class="text-red">Lihat semua daftar pelanggan</h6>-->
									<center>
										<q-btn
											flat
											size="sm"
											class="bg-orange-8 text-white"
											@click="selectCustomerDialog = true"
											>Pilih Pelanggan</q-btn
										>
										<span style="font-size: 10px; margin: 0; padding: 0 10px"
											>atau</span
										>
										<q-btn
											outline
											size="sm"
											color="black"
											@click="addCustomer = true"
											>Tambah Pelanggan</q-btn
										>
									</center>
								</div>
							</div>
						</div>
					</div>
					<div style="background-color: white; margin-bottom: 5px">
						<div class="row q-pa-lg items-center">
							<div class="col sender-details">
								<h6
									style="font-size: 14px; margin: 0 0 8px 0; font-family: 'Open Sans'; line-height: 18px; font-weight: bold"
								>
									Keterangan Pengirim :
								</h6>
								<div v-if="!shipAsDropshipper">
									<div style="font-weight: bold">
										Nama Pengirim:
									</div>
									<q-banner dense class="sender-name">{{
										globalState.userProfile.name
									}}</q-banner>
									<div style="font-weight: bold">
										No Handphone:
									</div>
									<q-banner dense class="sender-name">{{
										globalState.userProfile.phone
									}}</q-banner>
								</div>
								<div v-else>
									<q-input
										type="text"
										v-model="shipment.dropshipperName"
										color="orange-8"
										dense
										class="bg-grey-2 q-mb-sm"
										outlined
										label="Nama Dropship"
									/>
									<q-input
										type="text"
										v-model="shipment.dropshipperPhoneNumber"
										color="orange-8"
										dense
										class="bg-grey-2 q-mb-sm"
										outlined
										label="Nomor HP Dropshipper"
									/>
								</div>
								<div>
									<input
										v-model="shipAsDropshipper"
										type="checkbox"
										name="useStoreName"
										style="margin: 5px"
									/>
									<label for="useStoreName">Kirim Sebagai Dropshipper</label>
								</div>
							</div>
						</div>
					</div>
					<div style="background-color: white; margin-bottom: 5px">
						<div class="row q-pa-lg items-center">
							<div class="col">
								<h6
									style="font-size: 14px; margin: 0 0 8px 0; font-family: 'Open Sans'; line-height: 18px; font-weight: bold"
								>
									Keterangan Penerima:
								</h6>
								<template
									v-if="dataCustomerSelected !== null"
									class="sender-name"
								>
									<h6
										style="font-size: 12px; margin: 0; font-family: 'Open Sans'; line-height: 18px; font-weight: bold"
										class="text-blue-10"
									>
										{{ dataCustomerSelected.customer_name }} -
										{{ dataCustomerSelected.customer_phone }}
									</h6>
									<h6
										style="font-size: 12px; margin: 0; font-family: 'Open Sans'; line-height: 18px"
									>
										{{ dataCustomerSelected.address_detail }}, Kecamatan
										{{ dataCustomerSelected.subdistrict.subdistrict }},
										{{
											dataCustomerSelected.city.type +
												" " +
												dataCustomerSelected.city.city
										}}, Provinsi {{ dataCustomerSelected.province.province }},
										{{ dataCustomerSelected.postal_code }}
									</h6>
								</template>
								<template v-else>Pelanggan Belum Dipilih</template>
							</div>
						</div>
					</div>
					<template v-if="dataCustomerSelected !== null">
						<div style="background-color: white; margin-bottom: 5px">
							<div
								class="row q-pa-xs q-px-md items-center"
								style="padding-top: 10px; padding-bottom: 10px"
							>
								<div class="col">
									<q-checkbox
										left-label
										dense
										v-model="resiOtomatis"
										color="orange-8"
										label="Input Resi Otomatis?"
										@input="emptyShipment"
									/>
								</div>
							</div>
						</div>
						<div style="background-color: white;">
							<div
								class="row q-pa-xs items-center"
								style="padding-top: 20px; padding-bottom: 20px"
							>
								<div class="col">
									<template v-if="resiOtomatis !== true">
										<h6
											style="font-size: 14px; margin: 0; padding: 0 20px 10px 20px; font-family: 'Open Sans'; line-height: 18px; font-weight: bold"
										>
											Perhitungan Ongkos Kirim:
										</h6>
										<center>
											<div class="q-px-md">
												<q-select
													dense
													outlined
													color="orange-8"
													options-dense
													v-model="shipment.courierSelected"
													:options="courierOptions"
													option-value="value"
													option-label="label"
													emit-value
													map-options
													label="Pilihan Ekspedisi"
													@input="getCostShipping"
												/>
											</div>
										</center>
										<!-- <q-btn-toggle
                      v-model="courierSelected"
                      unelevated
                      toggle-color="blue-grey-1"
                      @input="getCostShipping"
                      :options="[
                        {value: 'jne', slot: 'jne'},
                        {value: 'jnt', slot: 'jnt'},
                        {value: 'pos', slot: 'pos'}
                      ]"
                    >
                      <template v-slot:jne>
                        <div class="row items-center no-wrap">
                          <img src="~/assets/images/components/ekspedisi/jne.png" width="65" />
                        </div>
                      </template>

                      <template v-slot:jnt>
                        <div class="row items-center no-wrap">
                          <img src="~/assets/images/components/ekspedisi/jnt.png" width="65" />
                        </div>
                      </template>

                      <template v-slot:pos>
                        <div class="row items-center no-wrap">
                          <img src="~/assets/images/components/ekspedisi/pos.png" width="65" />
                        </div>
                      </template>
                    </q-btn-toggle>-->
										<!-- {{ dataCost.results[0].costs }} -->
										<br />
										<h6
											style="font-size: 14px; margin: 0; padding: 0 20px 10px 20px; font-family: 'Open Sans'; line-height: 18px; font-weight: bold"
											v-if="
												shipment.courierSelected !== '' &&
													dataCustomerSelected !== null
											"
										>
											Pilih tarif :
										</h6>
										<q-list dense>
											<q-item v-for="(cost, index) in dataCost" :key="index">
												<q-item-section side>
													<q-radio
														keep-color
														color="orange-8"
														v-model="serviceSelectedRadio"
														:val="cost"
														@input="onServiceSelectedRadio"
													/>
												</q-item-section>
												<!-- {{ dataCost.results.name }} -->
												<q-item-section>
													{{ shipment.courierName }} {{ cost.service }}
													<br />
													<span style="font-size: 10px">
														Rp{{ formatPrice(cost.cost[0].value) }}
														<span v-if="cost.cost[0].etd !== ''">{{
															"(Estimasi " + cost.cost[0].etd + " Hari)"
														}}</span>
													</span>
												</q-item-section>
											</q-item>
										</q-list>
									</template>
									<template v-else>
										<div class="row q-px-md">
											<div class="col-12">
												<q-input
													v-model="shipment.shippingCost"
													type="text"
													color="orange-8"
													dense
													class="bg-grey-2 q-mb-sm"
													outlined
													label="Ongkos Kirim"
												/>
												<q-input
													v-model="shipment.shippingCostCut"
													color="orange-8"
													type="text"
													dense
													class="bg-grey-2 q-mb-sm"
													outlined
													label="Potongan/Subsidi Ongkir"
												/>
											</div>
											<div class="col-12">
												<q-input
													v-model="shipment.autoReceiptNumber"
													color="orange-8"
													type="text"
													dense
													class="bg-grey-2 q-mb-sm"
													outlined
													placeholder="Silahkan Masukkan No Resi Otomatis Disini.."
													label="No Resi Otomatis"
													:disable="disableResiInput"
												/>
												<q-input
													v-model="shipment.autoReceiptCourier"
													color="orange-8"
													type="text"
													dense
													class="bg-grey-2 q-mb-sm"
													outlined
													placeholder="Silahkan Masukkan Nama Ekpedisi Disini.."
													label="Nama Ekspedisi"
													:disable="disableResiInput"
												/>
											</div>
										</div>
									</template>
								</div>
							</div>
						</div>
					</template>
				</div>

				<q-dialog v-model="selectCustomerDialog" position="bottom">
					<q-card style="min-width: 360px">
						<q-card-section>
							<div class="text-h6">Pilih Pelanggan</div>
						</q-card-section>

						<q-card-section>
							<template v-if="dataCustomers.length > 0">
								<q-scroll-area style="height: 350px; max-width: 360px;">
									<div
										style="border: 1px solid #bdbdbd; border-radius: 5px; padding: 12px; margin-bottom: 20px"
										v-for="(customer, index) in dataCustomers"
										:key="index"
									>
										<div class="row">
											<div class="col-xs-8"></div>
											<div class="col-xs-4">
												<q-btn
													flat
													dense
													size="xs"
													class="bg-orange-8 text-white float-right text-capitalize"
													@click="selectCustomer(customer.id)"
													v-close-popup
													>Pilih Pelanggan</q-btn
												>
											</div>
										</div>
										<div class="row">
											<div class="col">
												<div class="row">
													<div class="col">
														<h6 class="text-list">
															<b>Nama Customer</b>
														</h6>
														<h6 class="text-list">
															<b>No Handphone</b>
														</h6>
														<h6 class="text-list">
															<b>Provinsi</b>
														</h6>
														<h6 class="text-list">
															<b>Kota/Kabupaten</b>
														</h6>
														<h6 class="text-list">
															<b>Kecamatan</b>
														</h6>
														<h6 class="text-list">
															<b>Detail Alamat</b>
														</h6>
													</div>
													<div class="col">
														<h6 class="text-list">
															<b>:</b>
															{{ customer.customer_name }}
														</h6>
														<h6 class="text-list">
															<b>:</b>
															{{ customer.customer_phone }}
														</h6>
														<h6 class="text-list">
															<b>:</b>
															{{ customer.province.province }}
														</h6>
														<h6 class="text-list">
															<b>:</b>
															{{
																customer.city.type + " " + customer.city.city
															}}
														</h6>
														<h6 class="text-list">
															<b>:</b>
															{{ customer.subdistrict.subdistrict }}
														</h6>
														<h6 class="text-list">
															<b>:</b>
															{{ customer.address_detail }}
														</h6>
													</div>
												</div>
											</div>
										</div>
									</div>
								</q-scroll-area>
							</template>
							<template v-else>
								<div class="q-pa-md">
									<center>
										<img
											src="https://image.flaticon.com/icons/svg/145/145859.svg"
											width="75"
											style="margin-bottom: 25px"
										/>
										<div class="text-bold text-black">Belum Ada Pelanggan</div>
										<div class="text-bold text-grey">
											Silahkan Tambah Pelanggan terlebih dahulu
										</div>
									</center>
								</div>
							</template>
						</q-card-section>

						<!-- <q-card-actions align="right">
              <q-btn flat label="OK" color="primary" v-close-popup />
            </q-card-actions>-->
					</q-card>
				</q-dialog>
			</q-page>

			<q-dialog v-model="addCustomer" position="bottom">
				<q-card style="min-width: 360px">
					<!-- <q-card-section>
            <div class="text-h6">Tambah Pelanggan</div>
          </q-card-section>-->

					<q-card-section>
						<q-input
							type="text"
							color="orange-8"
							v-model="customerName"
							label="Nama Pelanggan"
							dense
							outlined
							style="margin-bottom: 5px"
						/>
						<q-input
							type="text"
							color="orange-8"
							v-model="customerPhone"
							label="No Handphone"
							dense
							outlined
							style="margin-bottom: 5px"
						/>
						<q-input
							type="textarea"
							color="orange-8"
							v-model="addressDetail"
							label="Detail Alamat"
							dense
							outlined
							style="margin-bottom: 5px"
						/>
						<q-select
							color="orange-8"
							dense
							outlined
							v-model="provinceID"
							:options="dataProvince"
							option-value="id"
							option-label="province"
							label="Provinsi"
							emit-value
							map-options
							@input="getCity"
							style="margin-bottom: 5px"
						/>
						<q-select
							color="orange-8"
							dense
							outlined
							v-model="cityID"
							:options="dataCity"
							option-value="id"
							:option-label="opt => opt.type + ' ' + opt.city"
							label="Kota/Kabupaten"
							emit-value
							map-options
							@input="getSubdistrict"
							style="margin-bottom: 5px"
						/>
						<q-select
							color="orange-8"
							dense
							outlined
							v-model="subdistrictID"
							:options="dataSubdistrict"
							option-value="id"
							option-label="subdistrict"
							label="Kecamatan"
							emit-value
							map-options
							style="margin-bottom: 5px"
						/>
						<q-input
							type="text"
							color="orange-8"
							v-model="postalCode"
							label="Kode POS (Opsional)"
							dense
							outlined
							style="margin-bottom: 5px"
						/>
					</q-card-section>

					<q-card-actions class="q-px-md">
						<q-btn
							flat
							label="Simpan Data"
							color="white"
							class="bg-orange-8 text-capitalize full-width"
							@click="addNewCustomer"
							v-close-popup
						/>
					</q-card-actions>
				</q-card>
			</q-dialog>
		</q-page-container>
	</q-layout>
</template>

<script>
import { mapState } from "vuex";
import {
	cartService,
	getCustomerUrl,
	showCustomerUrl,
	addNewCustomerUrl,
	getProvinceUrl,
	getCityUrl,
	getSubdistrictUrl,
	getCostShippingUrl,
	getHeader
} from "src/config";

export default {
	name: "Shipping",
	data() {
		return {
			// user's Store
			shipAsDropshipper: false,
			// Detail Address
			dataCustomerSelected: null,
			courierOptions: [
				{
					value: "jne",
					label:
						'<div class="row"><div class="self-center" style="margin-right: 10px">JNE</div> <img src="https://i.imgur.com/hhJyhyS.png" height="20" /></div>'
				},
				{
					value: "jnt",
					label:
						'<div class="row"><div class="self-center" style="margin-right: 10px">J&T</div> <img src="https://i.imgur.com/TNdagJs.png" height="20" /></div>'
				},
				{
					value: "pos",
					label:
						'<div class="row"><div class="self-center" style="margin-right: 10px">POS</div> <img src="https://i.imgur.com/2VEBPMp.png" height="20" /></div>'
				}
			],

			// Form Add New Customer
			customerName: "",
			customerPhone: "",
			addressDetail: "",
			provinceID: null,
			cityID: null,
			subdistrictID: null,
			postalCode: "",
			// Data Shipping
			dataCustomers: [],
			dataProvince: [],
			dataCity: [],
			dataSubdistrict: [],
			dataCost: [],
			// Toogle Dialog
			addCustomer: false,
			selectCustomerDialog: false,
			resiOtomatis: false,
			serviceSelectedRadio: null,
			cartData: {
				cart_detail: [],
				total_amount: 0
			},
			shipment: {
				dropshipperName: "",
				dropshipperPhoneNumber: ""
			}
		};
	},
	computed: {
		...mapState(["globalState"]),
		allowOrder: function() {
			if (
				this.resiOtomatis == true &&
				this.shipment.autoReceiptCourier &&
				this.shipment.autoReceiptNumber
			) {
				return "resiOtomatis";
			} else if (this.resiOtomatis == false && this.serviceSelectedRadio) {
				return "courierService";
			}
			return false;
		},
		disableResiInput: function() {
			if (
				this.resiOtomatis == true &&
				this.shipment.shippingCost &&
				this.shipment.shippingCostCut
			) {
				return false;
			}
			return true;
		}
	},
	async created() {
		if (Object.keys(this.globalState.userProfile).length === 0) {
			await this.$store.dispatch("globalState/getUserProfile");
		}
		await this.getCartData();
		await this.getCustomers();
		await this.getProvince();
	},
	mounted() {},
	methods: {
		async getCartData() {
			// set allow order to false initially
			this.$q.loading.show({
				backgroundColor: "grey",
				message: "<b>Mohon Tunggu..</b>",
				messageColor: "black"
			});
			let tempCart, cartRes;

			try {
				cartRes = await this.$axios({
					method: "get",
					url: `${cartService}/get-cart/${this.globalState.userProfile.id}`,
					headers: getHeader()
				});
			} catch (error) {
				console.log("error fetching cart");
				console.log(error.message);
			}
			tempCart = cartRes.data.data;
			this.cartData =
				tempCart && tempCart.cart_detail.length > 0
					? tempCart
					: {
							cart_detail: [],
							total_amount: 0
					  };

			this.$q.loading.hide();
		},
		getCustomers() {
			this.$axios
				.get(getCustomerUrl + "/" + this.globalState.userProfile.id, {
					headers: getHeader()
				})
				.then(response => {
					if (response.status === 200) {
						this.dataCustomers = response.data.data;
					}
				})
				.catch(error => {
					if (error.response) {
						console.log(error.response);
					}
				});
		},
		selectCustomer(id) {
			this.$axios
				.get(showCustomerUrl + "/" + id, { headers: getHeader() })
				.then(response => {
					if (response.status === 200) {
						this.dataCustomerSelected = response.data.data;

						// Set Ekspedisi

						this.getCostShipping();
					}
				})
				.catch(error => {
					if (error.response) {
						console.log(error.response);
					}
				});
		},
		getProvince() {
			this.$axios
				.get(getProvinceUrl, { headers: getHeader() })
				.then(response => {
					if (response.status === 200) {
						this.dataProvince = response.data.data;
					}
				})
				.catch(error => {
					if (error.response) {
						console.log(error.response);
					}
				});
		},
		getCity() {
			(this.cityID = null),
				this.$axios
					.get(getCityUrl + "/" + this.provinceID, { headers: getHeader() })
					.then(response => {
						if (response.status === 200) {
							this.dataCity = response.data.data;
						}
					})
					.catch(error => {
						if (error.response) {
							console.log(error.response);
						}
					});
		},
		getSubdistrict() {
			(this.subdistrictID = null),
				this.$axios
					.get(getSubdistrictUrl + "/" + this.cityID, { headers: getHeader() })
					.then(response => {
						if (response.status === 200) {
							this.dataSubdistrict = response.data.data;
						}
					})
					.catch(error => {
						if (error.response) {
							console.log(error.response);
						}
					});
		},
		addNewCustomer() {
			let customerData = new FormData();
			customerData.set("customer_name", this.customerName);
			customerData.set("customer_phone", this.customerPhone);
			customerData.set("address_detail", this.addressDetail);
			customerData.set("province_id", this.provinceID);
			customerData.set("city_id", this.cityID);
			customerData.set("subdistrict_id", this.subdistrictID);
			customerData.set("postal_code", this.postalCode);
			customerData.set("user_id", this.globalState.userProfile.id);

			this.$axios
				.post(addNewCustomerUrl, customerData, { headers: getHeader() })
				.then(response => {
					if (response.status === 200) {
						this.$q.notify({
							position: "top",
							color: "dark",
							message: "Pelanggan Berhasil Ditambahkan"
						});
						this.getCustomers();
						this.customerName = "";
						this.customerPhone = "";
						this.addressDetail = "";
						this.provinceID = null;
						this.cityID = null;
						this.subdistrictID = null;
						this.postalCode = "";
					}
				})
				.catch(error => {
					if (error.response) {
						console.log(error.response);
					}
				});
		},
		getCostShipping() {
			if (this.dataCustomerSelected === null) {
				alert("Silahkan Pilih Pelanggan dulu");
			} else {
				let postCost = new FormData();
				postCost.set("origin", 108);
				postCost.set("originType", "city");
				postCost.set("destination", this.dataCustomerSelected.subdistrict_id);
				postCost.set("destinationType", "subdistrict");
				postCost.set("weight", Number(this.cartData.total_weight));
				postCost.set("courier", this.shipment.courierSelected);

				this.$axios
					.post(getCostShippingUrl, postCost, {
						headers: getHeader()
					})
					.then(response => {
						if (response.status === 200) {
							this.dataCost = response.data.rajaongkir.results[0].costs;
							this.shipment.courierName =
								response.data.rajaongkir.results[0].name;
							this.serviceSelectedRadio = null;
							this.shipment.serviceSelected = null;
							this.shipment.shippingCost = null;
						}
					})
					.catch(error => {
						if (error.response) {
							console.log(error.response);
						}
					});
			}
		},
		onServiceSelectedRadio() {
			this.shipment.serviceSelected = this.serviceSelectedRadio.service;
			this.shipment.shippingCost = this.serviceSelectedRadio.cost[0].value;
		},
		formatPrice(value) {
			let val = (value / 1).toFixed(0).replace(".", ",");
			return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".");
		},

		reviewOrder() {
			// has chosen as dropshipper but didnt fille out dropshipper form
			if (
				this.shipAsDropshipper &&
				(!this.shipment.dropshipperName ||
					!this.shipment.dropshipperPhoneNumber)
			) {
				this.$q.notify({
					position: "top",
					color: "red",
					message: "Mohon Lengkapi Form Dropshipper"
				});
				return -1;
			}

			if (["resiOtomatis", "courierService"].includes(this.allowOrder)) {
				if (this.allowOrder == "resiOtomatis") {
					this.shipment.shippingCost -= this.shipment.shippingCostCut;
					delete this.shipment.shippingCostCut;
				}
				this.$router.push({
					name: "OrderSummary",
					params: {
						shipment: {
							...this.shipment,
							destinationId: this.dataCustomerSelected.id,
							type: this.allowOrder,
							shipAsDropshipper: this.shipAsDropshipper
						}
					}
				});
			} else {
				this.$q.notify({
					position: "top",
					color: "red",
					message: "Silakan Pilih Pelanggan dan Jasa Kurir"
				});
			}
		},
		emptyShipment() {
			this.dataCost = null;
			this.serviceSelectedRadio = null;
			this.shipment = {};
		}
	}
};
</script>

<style>
.sender-details {
	display: grid;
}

.sender-name {
	background: orange;
	color: white;
	border-radius: 5px;
	font-style: italic;
}

.no-store-name {
	background: orange;
	min-height: 0;
	color: white;
	/* padding: 0; */
}

.no-store-name .to-store {
	background: cornflowerblue;
}
</style>
