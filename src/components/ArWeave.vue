<template>
  <div>
    <div>
      <div><strong>Balance</strong></div>
      <div>{{ balance / Math.pow(10, 12) }} AR | {{ balance }} Winston</div>
      <div><strong>Sube un archivo</strong></div>
      <input type="file" name="upload" :onchange="onInput" />
      <div v-if="uploadTransactionTx">
        <strong>Tx:</strong> {{ uploadTransactionTx }}
      </div>
      <img v-if="fileToUpload" :src="fileToUpload" />
    </div>
    <hr />
    <div>
      <div><strong>Get image by Tx</strong></div>
      <input type="text" v-model="txIdToSearch" />
      <button @click="searchTxById">Buscar</button>
      <div>
        <img v-if="fileUploaded" :src="fileUploaded" />
      </div>
    </div>
  </div>
</template>

<script>
import Arweave from "arweave";
var Buffer = require("buffer/").Buffer;

export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  data() {
    return {
      ARWEAVE_KEY: "ARWEAVE_KEY",
      arweave: null,
      address: null,
      balance: 0,
      uploadTransactionTx: null,
      txIdToSearch: null,
      fileToUpload: null,
      fileUploaded: null,
      key: {
        kty: "RSA",
        n: "ucgAh2d61ARPKeJhnqfX3masNs2kpVSLBg5pNE7sOrFz7NrZqxzr5XY09yN044PM_xvh__XxPRREohq5zEKbdXsqJF2uueTSGR3LzQ4CGEDTR7CuoIA33kkSYOz7FnSn1YpCYKl9ZuAjsr-ajSMDEeQYPgIdWDTEPWvHlJsZ3smVjQ119IU-C6tmFH_dnstLdD-OmVcrYUnNtRROJVnU1bSZ1XdUq4wbLXvK1EYhkdnMa8fln1jQGPY1Ty1mebiWXUoDk8NyOE2Y7AFsSHEM9ymzfs19IaAZNUgW5xapo2iq7pY9bcFJBKFcfCz69lZ1cq0IEeVRKN7emsS3kyBxPTtCspJrrH1QNO_WDss15YhWx1rUeY5zfL4qM5-3RaJ4StzadxfDpe7eElOQ8qaDWpJpH8e9xpGXRVmRXi61wyecpd7cA_1G_vlbZkhJsNFCFpLdPQCw2wmmrSl6cPJTwPm0rCTlKJCwDE-hIPIO4LT61YkVF-aRp-hZbO69tXdrOCcGvoEeMrH1gig7BQ0B6r4H8sCSRw6mtB-u3ax82MuSXtFDEppIM2mHL26LxIOZSZDktEa0wuoKFq44MaqBku73EXnZ98laeXrNeYw4ATIfx60glEwduc2lJj0S9svm8BHQOqDCia9oR7euM6FXzpGvxhQAsm0t2Pq1Cr6aNi0",
        e: "AQAB",
        d: "nDZBy5kFPLUK44sIzmEPMGBgugwcPzBhXYxMhcE-_PMtKKG0mR7Oc7j7PDtCE_RFMNT_KTxhxhv-wdd7FveCxDv16XIkcwamapwVO6xlsoL_pK45h5eIYo5Kt9lMH0uju_hva9vYJ-Kb4bcee0JPWIHUqH8asYpc39B6lsf77srNxzbkUeCflSxfvyjs7pK-NVcjzmfvf_SQ1YvnUZzBS5h6KXYXac97IrmOiQMXQw5BxvbkayJAiFyt4o-oNP0y4epUK7ERn6oPxZJEvHzijAkydpLOfrGhwJ2HNHRn25Es-US3DkyR2yTbIUapFu0IAI7h1DpgDd-oibaTLVEAJS8j67P6pVPvjnpT0twL9DY47ilD9_y86i5juSqSzaaYIH5FlFbF5L0jZ7WSO612379tAGGvBCc8i-QAeJQGNEm6jICmSV_sdWZeBpw6UESx8xSLwi3TE66eA9XkYuWP9d2M_963Agfc2w7LFdjHOfMiMwmXxzqc20d6fvNPZ_Zx8Z1bTX__nT8Byie9PNrNO_UQElBsieWatCzDvhjMJ4PDqNQADXBWX0sw7PwUGok-XoT7Qi4kEujXRXmFDTFGQEV0f_qazRAEV9HxizFCbABWjvpuJeFHMkQk3-v3S9nIDEn3zQXDt7TB89W5ZRdg8QHiV77llri5_ZdE_PXRvzk",
        p: "5gO-Zk_CZneLJ71Foqf9kZMpHowJ4bMyrAPjR6lHJQj0-A4P2mJtGKtBZCGRqU3UR4lyqhpji1gAkX6_gAktymsMEax4kOeisAcJpGG9tqpBbK-k_vlNyFQPjBFedqa-oFkUjiWKwrcZjZSCQh9t8ZxJbo_ujnL6pgfrW8NxiRdoWf0dYZvuEZXRaiRhWuc4slRQcpvwVK8ggz8ui_yV_9706TpEVH7rAtgPrcs-nfZUhcmj02raO4ZVMqq5EJr-VpmCJau0WJQ-vzJWt4fHTVVBX2h8aNRryFK3Pgf9SKFW3KvLyw1hH0x-WPKe1Gx8tsi-xV_ehy93O40KCzFejw",
        q: "zsT8CHnJVco9ym1xCDn8p5HvtX7BhuBGTUnazMCITxUMS-JA2NH5CeTb8znknALtrvIS3D_qiP-ssQwo56wD5s01XhZdiuBQ5EDfrDLj19NcvgxDT2nQkjtUcn5RRe5_AHlrZryjyLgEZ1H5wDMASOkrw6cw-HBWZ0xgG7IapqMVdc7GaW8Czgj_jXOwGWmcfSDa0g0So9UsINrHkc5LzvwFhEe4DjnRwTUCWdjc8vvY2P_YUNhY4LbPM7hT-Uu-tqaxg-fpgeHVlBDQG5A2UCXs2IYwXQ02JUAhMB9eog3VG5dVGajxaILXd14D5p5UyYpN4vJNzBrp4BJ-GaV9gw",
        dp: "EYqbjKXsh2_6ds9ibiMtnVqBukinwESwSpoJTT_Fozdppzk7UEZNV6JH3GELAMRkugfrbLmSed_-OxpVdGg-y78aLobesv5XU-FVhOnHVBTr5GQIy0EsA2kVvnLcp2PUCdqBTM3kJTFdi4SV4oEPG2v11a64XLi8EMlt05O2JuRYGvTIttbzcvff_p5DpnEXy9HOSM9Hps2sRxYccKSUs-zG9Lpy15bOSWs81t43KkjM7V8RVWBCwGNgP_rxHikT2HrgiAruOAsmNeKa5mg-dNFZqPMI-d2pUp-sRdjKIi3bt_yKEDQ3AfINeK8YGc8kO7RXiCnliJ3AFBSPfYcIVQ",
        dq: "TpGtj27voWjLGAuEIvMd3XLn2liDI0Q_kojY2mrioOJHnOIpb_pBno_XQFIkW2AFfy-_GPA0p8A3l8MeHAJSLTaW69ylyOq58jHwjFd-GnnXSOQSA7AasCZZTXRQX7ljiwOYWx6dQd0i1zvgIjd_CTWJCrySMCVHv9LWxk9kLyPTOMPMwy2KrE3hBZgLN2zZKPb-D0kmZxjOvuFDalUwm0NKuVwjRUyVNsx0yz1LoA0w9iwpv3amNyVgELAfQKCWpIMs-wl5wn_VD4FgAslGyifPGuvKnuExTPBUbBvPcta8vtI_ZRO8P9FQHKyd4NkTMM4cEHuwMjmb2yhLwGURDw",
        qi: "kCLfQBaBUQj6N_D1LBQO5ylfi6Cgn4yULyypDOMe1Ohi92wmkzfq6WNw8T9ONy0GlzIocqF6hcWwgnb7qT-C9fHdOakTQ0wq4TFQca2lv7iatA095edAAgzndtHPXcWLvftdAIyiPjZdHHlcs1MHGROLWFUoVTTPvwQrBjznJk3StR2Gw0YKtdxj7OcDsv86meW4Yb8FnZLz-f58a7uWiIYyzOPmQ1UfIryei_FLwKEXgWxTN_guw80eOdHLwVgXzYadZHC8lhyb_i2cqErzpfXuTjCKw0z46uGVzimohXIlu_Xyk090MugkxzogKvRB3A5xa7KzwNbWLTWt2RbHHQ",
      },
    };
  },
  async mounted() {
    this.arweave = Arweave.init({});

    // const key = await this.arweave.wallets.generate();
    // if (!localStorage.getItem(this.ARWEAVE_KEY)) {
    //   localStorage.setItem(this.ARWEAVE_KEY, JSON.stringify(key));
    // }
    localStorage.setItem(this.ARWEAVE_KEY, JSON.stringify(this.key));
    this.address = await this.getAddress();
    this.balance = await this.getBalance(this.address);

    // {
    //     "kty": "RSA",
    //     "n": "3WquzP5IVTIsv3XYJjfw5L-t4X34WoWHwOuxb9V8w...",
    //     "e": ...
  },
  methods: {
    async getAddress() {
      const arweaveKey = JSON.parse(localStorage.getItem(this.ARWEAVE_KEY));
      const address = await this.arweave.wallets.jwkToAddress(arweaveKey);
      console.log("address", address);
      return address;
    },
    async getBalance(address) {
      const balance = await this.arweave.wallets.getBalance(address);
      return balance;
    },
    onInput(event) {
      const reader = new FileReader();
      reader.onloadend = async () => {
        let transaction = await this.arweave.createTransaction(
          {
            data: Buffer.from(reader.result, "utf8"),
          },
          this.key
        );
        console.log("transaction", transaction);
        await this.arweave.transactions.sign(transaction, this.key);
        let uploader = await this.arweave.transactions.getUploader(transaction);

        while (!uploader.isComplete) {
          await uploader.uploadChunk();
          console.log(
            `${uploader.pctComplete}% complete, ${uploader.uploadedChunks}/${uploader.totalChunks}`
          );
        }

        console.log("uploader", uploader);
        this.uploadTransactionTx = uploader.transaction.id;
      };
      reader.readAsArrayBuffer(event.target.files[0]);
    },
    async searchTxById() {
      const status = await this.arweave.transactions.getStatus(
        this.txIdToSearch
      );
      console.log("status", status);
      if (status.confirmed) {
        const buffer = await this.arweave.transactions.getData(
          this.txIdToSearch,
          { decode: true }
        );

        var blob = new Blob([buffer], { type: "application/octet-binary" });
        var reader = new FileReader();
        reader.onload = (evt) => {
          const dataurl = evt.target.result;
          const base64 = dataurl.substr(dataurl.indexOf(",") + 1);
          console.log("base64", base64);
          this.fileUploaded = `data:image/png;base64, ${base64}`;
        };
        reader.readAsDataURL(blob);

        // console.log("buffer", buffer, buffer.toString("base64"));
      } else {
        alert("Transaction not confirmed yet");
      }
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
