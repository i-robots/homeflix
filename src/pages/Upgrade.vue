<template>
  <div class="column q-gutter-sm q-pa-md">
    <div class="flex flex-center">
      <q-btn @click="home"> {{ "<" }} Back </q-btn>
      <p class="text-h6 col-1 q-m-8" style="margin-left: 2em">
        Upgrade Homeflix Account
      </p>
    </div>
    <div
      style="text-align: center"
      id="paypal-button-container-P-5M317867GY346401VMKHBJSQ"
    ></div>
    <div id="sucessId" style="margin-top: 1em" class="hidden">
      <img src="https://i.ibb.co/9cZ9Rn2/1.gif" />
      <p style="color: green">you have successfully subscribed!</p>
    </div>
  </div>
</template>

<script>
import { defineComponent, ref } from "vue";
export default defineComponent({
  name: "UpgradePage",
  setup() {
    const success = ref(false);
    return {
      success,
    };
  },
  mounted() {
    let paypalscript = document.createElement("script");
    paypalscript.setAttribute(
      "src",
      "https://www.paypal.com/sdk/js?client-id=AeqArxNeMPCSijnxXVQwKQVtMiyleck7lKrl7LUkl-mTwk6s-U_qkkHy4SWywcEMN__xEva2l_2BW5TN&vault=true&intent=subscription"
    );
    paypalscript.setAttribute("data-sdk-integration-source", "button-factory");
    document.body.appendChild(paypalscript);
    let script2 = document.createElement("script");
    setTimeout(() => {
      script2.innerHTML = `
      paypal.Buttons({
        style: {
          shape: "pill",
          color: "gold",
          layout: "vertical",
          label: "subscribe",
        },
        createSubscription: function (data, actions) {
          return actions.subscription.create({
            plan_id: "P-5M317867GY346401VMKHBJSQ",
          });
        },
        onApprove: function (data, actions) {
          fetch("https://homeflix-app.herokuapp.com/api/user/upgrade", {
              method: "POST",
              headers: { "Content-Type": "application/json" , "Authorization": localStorage.getItem("token")} ,
              body: JSON.stringify({
                subscription_id: data.subscriptionID,
                plan_id: "P-5M317867GY346401VMKHBJSQ",
              }),
            })
              .then((response) => {
                if(response.status === 200){
                  document.getElementById("sucessId").classList.remove("hidden");
                } else {
                  alert('subscription failed')
                }
              })
              .catch((error) => {
                console.error(error);
              });
        },
      })
      .render("#paypal-button-container-P-5M317867GY346401VMKHBJSQ");`;
      document.body.appendChild(script2);
    }, 2000);
  },
  methods: {
    home() {
      this.$router.push({
        name: "Home",
      });
    },
  },
});
</script>
<style scoped>
.hidden {
  display: none;
}
</style>
