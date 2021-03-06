<template>
  <div class="bg-gray-800-spotify flex-1  flex flex-col">
    <top-bar />
    <div class="content-spotify overflow-y-auto ">
      <div class="mx-auto container">
        <!-- <alert-component v-show="errorMSG.error" :errorMSG="errorMSG" /> -->
        <h2 class="mt-18 text-5xl font-semibold text-white">Profile</h2>
        <div class="mt-10">
          <h3 class="font-semiblod text-sm border-b border-gray-900 pb-2">
            <strong class="text-xl text-green-300">
              {{ user.fullName || user.username }}</strong
            >
            this is your profile...
          </h3>
          <div
            class="flex flex-wrap w-full mb-20 flex-col  items-center text-center"
          >
            <div class="flex flex-wrap -m-4 ">
              <div class="xl:w-2/5 md:w-1/2 p-4">
                <!-- here profile component -->
                <CardProfile2
                  class="flex flex-1"
                  :user="user"
                  :secrets="secrets"
                  @show-options="onShowOptions"
                />
                <!-- end -->
              </div>

              <!-- Info sections -->
              <div class="xl:w-1/4 md:w-1/2 p-4">
                <div class="border border-gray-700-spotify p-6 rounded-lg my-8">
                  <div
                    class="w-32 h-32 inline-flex items-center justify-center rounded-full bg-gray-800-spotify mb-4"
                  >
                    <img class="h-32 w-32" src="@/assets/imgs/creditcard.svg" />
                  </div>
                  <h2
                    class="text-lg text-white font-medium title-font mb-2 text-gray-700 hover:text-gray-300"
                  >
                    Update password
                  </h2>
                  <p class="leading-relaxed text-base text-gray-700">
                    Change password. This feature will be available to change
                    your password and encrypt all secrets for better security
                  </p>
                </div>
              </div>
              <!-- end sections -->
              <!-- UpdatePassword section -->
              <div v-if="showPanel" class="xl:w-1/4 md:w-1/2 p-4">
                <!-- sections -->
                <div class="border border-gray-700-spotify p-6 rounded-lg my-8">
                  <div
                    class="w-20 h-20 inline-flex items-center justify-center rounded-full bg-gray-800-spotify"
                  >
                    <img class="h-20 w-20" src="@/assets/imgs/creditcard.svg" />
                  </div>
                  <ValidationObserver v-slot="{ invalid }">
                    <form
                      @submit.prevent="changePassword()"
                      class="w-full max-w-sm my-8 items-center"
                    >
                      <div
                        class="flex items-center border-b border-b-2 border-green-800 py-2"
                      >
                        <i class="mr-2 fa fa-lock text-gray-500"></i>
                        <ValidationProvider
                          name="Old password"
                          rules="required"
                          v-slot="{ errors }"
                        >
                          <span class="text-red-400">{{ errors[0] }}</span>

                          <input
                            class="appearance-none bg-transparent border-none w-full text-gray-500 mr-3 py-1 px-2 leading-tight focus:outline-none"
                            type="password"
                            placeholder="Old Password..."
                            v-model="payload.oldPassword"
                          />
                        </ValidationProvider>
                      </div>
                      <div
                        class="flex items-center border-b border-b-2 border-green-800 py-2"
                      >
                        <i class="mr-2 fa fa-lock text-gray-500"></i>
                        <ValidationProvider
                          name="New password"
                          rules="required"
                          v-slot="{ errors }"
                        >
                          <span class="text-red-400">{{ errors[0] }}</span>

                          <input
                            class="appearance-none bg-transparent border-none w-full text-gray-500  mr-3 py-1 px-2 leading-tight focus:outline-none"
                            type="password"
                            placeholder="New Password..."
                            v-model="payload.newPassword"
                          />
                        </ValidationProvider>
                      </div>
                      <div class="flex flex-col items-center py-2">
                        <button
                          :disabled="invalid"
                          :class="
                            `text-green-500 hover:text-white hover:bg-green-500 border border-green-500 text-xs font-semibold rounded-full mt-3 px-4 py-1 leading-normal ${
                              invalid ? 'cursor-not-allowed opacity-50' : ''
                            }`
                          "
                          type="submit"
                        >
                          Update
                        </button>
                      </div>
                    </form>
                  </ValidationObserver>
                </div>
                <!-- end sections -->
              </div>
              <!-- end UpdatePassword section -->
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { UtilsService, UserService } from '@/services/'

import TopBar from '@/components/TopBarComponent'
import CardProfile2 from './components/OtherCardProfile'
export default {
  name: 'profile',
  components: {
    TopBar,
    CardProfile2
  },
  data: () => ({
    notification: {},
    user: {},
    secrets: {},
    payload: {},
    showPanel: null
  }),
  mounted() {
    this.getUserFromLocalStorage()
    this.getSecretsFromLocalStorage()
  },
  methods: {
    getUserFromLocalStorage() {
      this.user = UtilsService.getItemStorage('user')
    },
    getSecretsFromLocalStorage() {
      this.secrets = UtilsService.getItemStorage(
        `secrets_by_${this.user.username}`
      )
    },
    async changePassword() {
      try {
        let res = await UserService.changePassword(
          this.payload.oldPassword,
          this.payload.newPassword,
          this.user.token
        )
        if (res.status === 202) {
          this.$toaster.success(
            `Yours password update are ${res.data.result} redirecting to login`
          )
          this.$router.push({ name: 'login' })
        }
      } catch (err) {
        this.notification = err.response.data
        this.$toaster.error(
          `Error: ${this.notification.statusCode} \n ${this.notification.error}`
        )
      }
    },
    onShowOptions(data) {
      this.showPanel = data
    }
  }
}
</script>

<style lang="scss">
.content-spotify::-webkit-scrollbar {
  width: 8px;
  background-color: #181818;
}

.content-spotify::-webkit-scrollbar-thumb {
  border-radius: 8px;
  background-color: #535353;
}
.tooltip {
  @apply block z-50;

  .tooltip-inner {
    @apply bg-gray-900-spotify text-white rounded-md p-4;
  }

  .tooltip-arrow {
    @apply w-0 h-0 absolute border border-black border-solid z-10 m-4;
  }

  &[x-placement^='top'] {
    @apply mt-22;

    .tooltip-arrow {
      border-width: 5px 5px 0 5px;
      border-left-color: transparent !important;
      border-right-color: transparent !important;
      border-bottom-color: transparent !important;
      bottom: -5px;
      left: calc(50% - 5px);
      margin-top: 0;
      margin-bottom: 0;
    }
  }

  &[x-placement^='bottom'] {
    margin-top: 5px;

    .tooltip-arrow {
      border-width: 0 5px 5px 5px;
      border-left-color: transparent !important;
      border-right-color: transparent !important;
      border-top-color: transparent !important;
      top: -5px;
      left: calc(50% - 5px);
      margin-top: 0;
      margin-bottom: 0;
    }
  }

  &[x-placement^='right'] {
    margin-left: 5px;

    .tooltip-arrow {
      border-width: 5px 5px 5px 0;
      border-left-color: transparent !important;
      border-top-color: transparent !important;
      border-bottom-color: transparent !important;
      left: -5px;
      top: calc(50% - 5px);
      margin-left: 0;
      margin-right: 0;
    }
  }

  &[x-placement^='left'] {
    margin-right: 5px;

    .tooltip-arrow {
      border-width: 5px 0 5px 5px;
      border-top-color: transparent !important;
      border-right-color: transparent !important;
      border-bottom-color: transparent !important;
      right: -5px;
      top: calc(50% - 5px);
      margin-left: 0;
      margin-right: 0;
    }
  }

  &.popover {
    $color: #f9f9f9;

    .popover-inner {
      background: $color;
      color: black;
      padding: 24px;
      border-radius: 5px;
      box-shadow: 0 5px 30px rgba(black, 0.1);
    }

    .popover-arrow {
      border-color: $color;
    }
  }

  &[aria-hidden='true'] {
    visibility: hidden;
    opacity: 0;
    transition: opacity 0.15s, visibility 0.15s;
  }

  &[aria-hidden='false'] {
    visibility: visible;
    opacity: 1;
    transition: opacity 0.15s;
  }
}
.v-toaster {
  position: fixed;
  top: 50px;
  right: 0;
  z-index: 10000;
  width: 300px;
  padding-left: 10px;
  padding-right: 10px;
}
.v-toaster .v-toast {
  margin-bottom: 10px;
  transition: all 0.3s ease;
  border: 1px solid #454d5d;
  border-radius: 8px;
  color: #fff;
  display: block;
  padding: 1rem;
  background: rgba(69, 77, 93, 0.9);
  border-color: #454d5d;
}
.v-toaster .v-toast.v-toast-enter,
.v-toaster .v-toast.v-toast-leave-to {
  -webkit-transform: translate(100%);
  -ms-transform: translate(100%);
  transform: translate(100%);
}
.v-toaster .v-toast.v-toast-success {
  //   background: rgba(50, 182, 67, 0.9);
  //   border-color: #32b643;
  @apply bg-gray-700-spotify border-green-500 text-gray-300;
}
.v-toaster .v-toast.v-toast-warning {
  //   background: rgba(255, 183, 0, 0.9);
  //   border-color: #ffb700;
  @apply bg-gray-700-spotify border-yellow-500 text-gray-300;
}
.v-toaster .v-toast.v-toast-info {
  //   background: rgba(91, 192, 222, 0.9);
  //   border-color: #5bc0de;
  @apply bg-gray-700-spotify border-blue-500 text-gray-300;
}
.v-toaster .v-toast.v-toast-error {
  //   background: rgba(232, 86, 0, 0.9);
  //   border-color: #e85600;
  @apply bg-gray-700-spotify border-orange-500 text-gray-300;
}
.v-toaster .v-toast.v-toast-primary {
  //   background: rgba(66, 139, 202, 0.9);
  //   border-color: #428bca;
  @apply bg-gray-700-spotify border-indigo-500 text-gray-300;
}
.v-toaster .v-toast .v-toast-btn-clear {
  background: transparent;
  border: 0;
  color: currentColor;
  opacity: 0.45;
  text-decoration: none;
  float: right;
  cursor: pointer;
}
.v-toaster .v-toast .v-toast-btn-clear:hover {
  opacity: 0.85;
}
.v-toaster .v-toast .v-toast-btn-clear:before {
  content: '\2715';
}
@media (max-width: 300px) {
  .v-toaster {
    width: 100%;
  }
}
</style>
