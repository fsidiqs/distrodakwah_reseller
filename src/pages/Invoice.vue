<template>
  <q-layout view="hHh lpR fFf">
    <q-header elevated class="mobile-layout-on-desktop">
      <q-toolbar class="bg-distrodakwah text-white">
        <q-btn flat round dense to="/">
          <q-icon name="arrow_back" color="white" />
        </q-btn>
        <q-toolbar-title>
          <span style="font-size: 16px; font-weight: bold">Invoice</span>
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-footer
      class="bg-white text-black mobile-layout-on-desktop"
      style="border-top: 2px solid #eee"
      v-if="this.dataOrder.status === 'waiting_payment'"
    >
      <q-toolbar class="bg-white text-black">
        <q-space />
        <q-btn
          flat
          class="bg-orange-8 text-white full-width"
          @click="paymentConfirmation = true"
        >Konfirmasi Pembayaran</q-btn>
      </q-toolbar>
    </q-footer>

    <q-page-container class="mobile-layout-on-desktop">
      <q-page class="bg-white">
        <div class="bg-grey-3" style="height: 100%">
          <div style="background-color: white;; padding: 13px 0 10px 0">
            <div class="row q-px-md q-py-xs">
              <div class="col">
                <h5 class="title-text" style="padding-left: 8px">Ringkasan Pembayaran</h5>
              </div>
              <div class="col text-right">
                <router-link
                  :to="'/detailOrder/' + this.$route.query.orderID"
                  style="text-decoration: none;"
                >
                  <h5 class="link-text text-red">Lihat Detail</h5>
                </router-link>
              </div>
            </div>
            <div class="row q-px-lg items-center" style="padding-top: 15px; padding-bottom: 15px">
              <div class="col-xs-8">
                <h6
                  style="font-size: 12px; margin: 0; font-family: 'Open Sans'; line-height: 18px"
                >Total Harga Barang</h6>
                <h6
                  style="font-size: 12px; margin: 0; font-family: 'Open Sans'; line-height: 18px"
                >Ongkos Kirim</h6>
                <h6
                  style="font-size: 12px; margin: 0; font-family: 'Open Sans'; line-height: 18px"
                  v-if="dataOrder.voucher_id !== null"
                >Potongan Harga</h6>
                <!-- <h6 style="font-size: 12px; margin: 0; font-family: 'Open Sans'; line-height: 18px">Donasi</h6> -->
              </div>
              <div class="col-xs-4 text-right">
                <h6
                  style="margin: 0; font-size: 12px; line-height: 18px"
                  class="text-black"
                >Rp{{ formatPrice(dataOrder.total_amount) }}</h6>
                <h6
                  style="margin: 0; font-size: 12px; line-height: 18px"
                  class="text-black"
                >Rp{{ formatPrice(dataOrder.shipment_fee) }}</h6>
                <h6
                  style="margin: 0; font-size: 12px; line-height: 18px"
                  class="text-red"
                  v-if="dataOrder.voucher_id !== null"
                >-Rp{{ formatPrice( (dataOrder.total_amount + dataOrder.shipment_fee) * dataOrder.voucher_discount / 100) }}</h6>
                <!-- <h6 style="margin: 0; font-size: 12px; line-height: 18px" class="text-black">Rp600</h6> -->
              </div>
            </div>
            <center>
              <hr style="border: 1px solid #eee; width: 90%;" />
            </center>
            <div class="row q-px-lg">
              <div class="col">
                <h5
                  style="font-size: 21px; margin: 0; font-family: 'Open Sans'; font-weight: bold"
                >Total</h5>
              </div>
              <div class="col text-right">
                <h5
                  style="font-size: 21px; margin: 0; font-family: 'Open Sans'; font-weight: bold"
                >Rp{{ formatPrice(dataOrder.grand_total + dataOrder.code_unique) }}</h5>
              </div>
            </div>
            <div class="row q-px-lg items-center">
              <div class="col-xs-8">
                <h6
                  style="font-size: 12px; margin: 0; font-family: 'Open Sans'; line-height: 18px"
                >Kode Unik</h6>
              </div>
              <div class="col-xs-4 text-right">
                <h6
                  style="margin: 0; font-size: 12px; line-height: 18px"
                  class="text-black"
                >Rp-{{ formatPrice(dataOrder.code_unique) }}</h6>
              </div>
            </div>
            <hr style="border: none; height: 30px" />
            <div class="row q-px-lg">
              <div class="col">
                <h6
                  style="font-size: 14px; margin: 10px 0; font-family: 'Open Sans'; text-align: center;"
                >Total Yang Harus Kamu Transfer</h6>
                <h6
                  style="font-size: 36px; margin: 0 0 18px 0; font-family: 'Open Sans'; text-align: center; font-weight: bold"
                >
                  Rp{{ formatPrice(dataOrder.grand_total) }}
                  <q-btn @click="copyTotalTransfer" flat size="xs" class="bg-red text-white">Salin</q-btn>
                </h6>
                <h6
                  style="font-size: 12px; margin: 10px 0; font-family: 'Open Sans'; text-align: center; line-height: 16px"
                >Silahkan lakukan pembayaran melalui transfer bank ke salah satu rekening dibawah ini sebelum :</h6>
                <h6
                  class="text-center bg-yellow text-bold"
                  style="font-size: 11px; margin: 0"
                >{{ dataOrder.expired_time ? new Date(dataOrder.expired_time*1000).toLocaleString('id-ID', { dateStyle: 'full', timeZone: 'Asia/Jakarta' }) : '' }} pukul {{ dataOrder.expired_time ? new Date(dataOrder.expired_time*1000).toLocaleTimeString('en-GB') : '' }} WIB (1x24 jam)</h6>
              </div>
            </div>
          </div>
          <div style="background-color: white;; padding: 13px 0 10px 0">
            <div class="row q-px-lg">
              <div class="col">
                <div class="row-accordion">
                  <div class="col-accordion">
                    <div class="tabs">
                      <div class="tab">
                        <input type="checkbox" id="chck1" class="accordion-input" />
                        <label class="tab-label" for="chck1">Transfer Bank</label>
                        <div class="tab-content">
                          <q-list>
                            <q-item
                              v-for="(bank, index) in dataBank.filter(bank=>bank.account_category == 1)"
                              :key="index"
                            >
                              <q-item-section side>
                                <img :src="bank.bank_image" width="50" />
                              </q-item-section>

                              <q-item-section side>
                                {{ bank.account_number }}
                                <br />
                                <span style="font-size: 10px">A.N {{ bank.account_name }}</span>
                              </q-item-section>

                              <q-item-section class="fixed-right absolute-right">
                                <q-btn
                                  @click="copyAccountNumber(bank.account_number)"
                                  flat
                                  size="xs"
                                  class="bg-red text-white"
                                  style="width: 60px;"
                                >Salin</q-btn>
                              </q-item-section>
                            </q-item>
                          </q-list>
                        </div>
                      </div>
                      <div class="tab">
                        <input type="checkbox" id="chck2" class="accordion-input" />
                        <label class="tab-label" for="chck2">Virtual Account</label>
                        <div class="tab-content">
                          <q-list>
                            <q-item
                              v-for="(bank, index) in dataBank.filter(bank=>bank.account_category == 2)"
                              :key="index"
                            >
                              <q-item-section side>
                                <img :src="bank.bank_image" width="50" />
                              </q-item-section>

                              <q-item-section side>
                                {{ bank.account_number }}
                                <br />
                                <span style="font-size: 10px">A.N {{ bank.account_name }}</span>
                              </q-item-section>

                              <q-item-section class="fixed-right absolute-right">
                                <q-btn
                                  @click="copyAccountNumber(bank.account_number)"
                                  flat
                                  size="xs"
                                  class="bg-red text-white"
                                  style="width: 60px;"
                                >Salin</q-btn>
                              </q-item-section>
                            </q-item>
                          </q-list>
                        </div>
                      </div>
                      <a target="_blank" href="https://bit.ly/VADistroDakwah" class="tab" style="text-decoration:none;">
                        <input type="checkbox" class="accordion-input" />
                        <label class="tab-label">Halaman Panduan Pembayaran</label>
                      </a>
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <br />
          </div>
        </div>
      </q-page>
      <q-dialog v-model="paymentConfirmation" position="bottom">
        <q-card style="min-width: 360px">
          <q-card-section>
            <div class="text-h6">Konfirmasi Pembayaran</div>
          </q-card-section>

          <q-card-section>
            <q-select
              color="orange-8"
              dense
              outlined
              v-model="bankReceiver"
              :options="dataBank"
              option-value="bank_name_tmp"
              option-label="bank_name_tmp"
              label="Bank Tujuan"
              emit-value
              map-options
              style="margin-bottom: 5px"
            />
            <q-input
              type="text"
              color="orange-8"
              v-model="bankSender"
              label="Transfer Dari Bank"
              dense
              outlined
              style="margin-bottom: 5px"
            />
            <q-input
              type="text"
              color="orange-8"
              v-model="senderName"
              label="Nama Pengirim"
              dense
              outlined
              style="margin-bottom: 5px"
            />
            <q-input
              type="number"
              color="orange-8"
              v-model="totalTransfer"
              label="Total Transfer"
              dense
              outlined
              style="margin-bottom: 5px"
            />
            <div
              class="q-field row no-wrap items-start bg-grey-2 q-mb-sm q-input q-field--outlined q-field--float q-field--dense"
            >
              <div class="q-field__inner relative-position col self-stretch column justify-center">
                <div class="q-field__control relative-position row no-wrap text-orange-8">
                  <div
                    class="q-field__control-container col relative-position row no-wrap q-anchor--skip"
                  >
                    <flat-pickr
                      v-model="transferDate"
                      class="q-field__native"
                      placeholder="Tanggal Transfer"
                    ></flat-pickr>
                  </div>
                </div>
              </div>
            </div>
            <!-- <q-input dense outlined v-model="transferDate" color="orange-8" placeholder="Tanggal Transfer">
              <template v-slot:append>
                <q-icon name="event" class="cursor-pointer">
                  <q-popup-proxy ref="qDateProxy" transition-show="scale" transition-hide="scale">
                    <q-date v-model="transferDate" mask="YYYY-MM-DD" color="orange-8" />
                  </q-popup-proxy>
                </q-icon>
              </template>
            </q-input>-->
          </q-card-section>

          <q-card-actions class="q-px-md">
            <q-btn
              flat
              label="Konfirmasi Sekarang"
              color="white"
              class="bg-orange-8 text-capitalize full-width"
              @click="postPaymentConfirmation"
              v-close-popup
            />
          </q-card-actions>
        </q-card>
      </q-dialog>
    </q-page-container>
  </q-layout>
</template>

<script>
import Vue from "vue";
import {
  showOrderUrl,
  identityBankUrl,
  paymentConfirmationUrl,
  paymentConfirmOrderUrl,
  getHeader
} from "src/config";
import VueClipboard from "vue-clipboard2";
import flatPickr from "vue-flatpickr-component";
import "flatpickr/dist/flatpickr.css";
import { openURL } from "quasar";

Vue.use(VueClipboard);

export default {
  components: {
    flatPickr
  },
  data() {
    return {
      dataBank: [],
      dataOrder: [],
      // Payment Confirmation
      bankReceiver: "",
      bankSender: "",
      senderName: "",
      totalTransfer: 0,
      transferDate: "",
      // Toggle
      paymentConfirmation: false
    };
  },
  mounted() {
    this.getDataBank();
    this.getOrder();
  },
  methods: {
    getOrder() {
      this.$axios
        .get(showOrderUrl + "/" + this.$route.query.orderID, {
          headers: getHeader()
        })
        .then(response => {
          console.log(response);

          if (response.status === 200) {
            this.dataOrder = response.data.data;
          }
        })
        .catch(error => {
          if (error.response) {
            console.log(error.response);
          }
        });
    },
    getDataBank() {
      this.$axios
        .get(identityBankUrl, { headers: getHeader() })
        .then(response => {
          console.log(response);

          if (response.status === 200) {
            this.dataBank = response.data.data.map(bank => {
              if (bank.account_category == 2)
                bank.bank_name_tmp = bank.bank_name + "~Virtual Account";
              else bank.bank_name_tmp = bank.bank_name;
              return bank;
            });
          }
        })
        .catch(error => {
          if (error.response) {
            console.log(error.response);
          }
        });
    },
    postPaymentConfirmation() {
      let paymentConfirm = new FormData();

      paymentConfirm.set("invoice", this.dataOrder.invoice);
      paymentConfirm.set("bank_receiver", this.bankReceiver);
      paymentConfirm.set("bank_sender", this.bankSender);
      paymentConfirm.set("sender_name", this.senderName);
      paymentConfirm.set("total_transfer", this.totalTransfer);
      paymentConfirm.set("transfer_date", this.transferDate);
      paymentConfirm.set("order_id", this.dataOrder.id);

      this.$axios
        .post(paymentConfirmationUrl, paymentConfirm, { headers: getHeader() })
        .then(response => {
          if (response.status === 200) {
            this.getDataBank();
            this.getOrder();

            openURL(
              "https://wa.me/628170090597?text=Konfirmasi%20Pembayaran%0A" +
                "%0ANo%20Invoice:%20" +
                this.dataOrder.invoice +
                "%0ABank%20Penerima:%20" +
                this.bankReceiver +
                "%0ABank%20Pengirim:%20" +
                this.bankSender +
                "%0ANama%20Pengirim:%20" +
                this.senderName +
                "%0ATotal%20Transfer:%20" +
                this.totalTransfer +
                "%0ATanggal%20Transfer:%20" +
                this.transferDate +
                "%0A%0A*Jangan%20Lupa%20Kirim%20Bukti%20Pembayaran*"
            );

            this.bankReceiver = "";
            this.bankSender = "";
            this.senderName = "";
            this.totalTransfer = "";
            this.transferDate = "";
          }
        })
        .catch(error => {
          if (error.response) {
            console.log(error.response);
          }
        });
    },
    formatPrice(value) {
      let val = (value / 1).toFixed(0).replace(".", ",");
      return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".");
    },
    copyTotalTransfer: function() {
      this.$copyText(this.dataOrder.grand_total).then(
        function(e) {
          alert("Berhasil Disalin!");
          console.log(e);
        },
        function(e) {
          alert("Gagal Disalin!");
          console.log(e);
        }
      );
    },
    copyAccountNumber: function(accountNumber) {
      this.$copyText(accountNumber).then(
        function(e) {
          alert("Berhasil Disalin!");
          console.log(e);
        },
        function(e) {
          alert("Gagal Disalin!");
          console.log(e);
        }
      );
    }
  }
};
</script>

<style>
.title-text {
  font-family: "Open Sans";
  font-size: 13px;
  font-weight: bold;
  margin: 0;
}
.link-text {
  font-family: "Open Sans";
  font-size: 13px;
  margin: 0;
}

/* accordion */

.accordion-input {
  position: absolute;
  opacity: 0;
  z-index: -1;
}
.row-accordion {
  display: -webkit-box;
  display: flex;
}
.row-accordion .col-accordion {
  -webkit-box-flex: 1;
  flex: 1;
}
.row-accordion .col-accordion:last-child {
}

/* Accordion styles */
.tabs {
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 4px 4px -2px rgba(0, 0, 0, 0.5);
}

.tab {
  width: 100%;
  color: white;
  overflow: hidden;
}
.tab-label {
  display: -webkit-box;
  display: flex;
  -webkit-box-pack: justify;
  justify-content: space-between;
  padding: 1em;
  background: #fea500;
  font-weight: bold;
  cursor: pointer;
  /* Icon */
}
.tab-label::after {
  content: "\276F";
  width: 1em;
  height: 1em;
  text-align: center;
  -webkit-transition: all 0.35s;
  transition: all 0.35s;
}
.tab-content {
  max-height: 0;
  padding: 0 1em;
  color: #fea500;
  background: white;
  -webkit-transition: all 0.35s;
  transition: all 0.35s;
}
.tab-close {
  display: -webkit-box;
  display: flex;
  -webkit-box-pack: end;
  justify-content: flex-end;
  padding: 1em;
  font-size: 0.75em;
  background: #fea500;
  cursor: pointer;
}

input:checked + .tab-label::after {
  -webkit-transform: rotate(90deg);
  transform: rotate(90deg);
}
input:checked ~ .tab-content {
  max-height: 100vh;
  padding: 0.4em;
}
</style>
