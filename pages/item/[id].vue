<template>
  <MainLayout>
    <div id="ItemPage" class="mt-4 max-w-[1200px] mx-auto px-2">
      <div class="md:flex gap-4 justify-between mx-auto w-full">
        <div class="md:w-[40%]">
          <img v-if="currentImage" class="rounded-lg object-fit" :src="currentImage">
          <div v-if="images[0] !== ''" class="flex items-center justify-center mt-2">
            <div v-for="image in images">
              <img @mouseover="currentImage = image" @click="currentImage = image" width="70"
                class="rounded-md object-fit border-[3px] cursor-pointer"
                :class="currentImage === image ? 'border-[#FF5353]' : ''" :src="image">
            </div>
          </div>
        </div>
        <div class="md:w-[60%] bg-white p-3 rounded-lg">
          <div v-if="product && product.data">
            <p class="mb-2">{{ product.data.title }}</p>
            <p class="font-light text-[12px] mb-2">{{ product.data.description }}</p>
          </div>
          <div class="flex items-center pt-1.t">
            <span class="h-4 min-w-4 rounded-full p-0.5 bg-[#FFD000] mr-2">
              <Icon name="material-symbols:star-rounded" class="-mt-[17px]" size="12" />
            </span>
            <p class="text-[#FF5353]">Extra 5% off</p>
          </div>
          <div class="flex items-center justify-start my-2">
            <Icon color="#FF5353" name="ic:baseline-star" />
            <Icon color="#FF5353" name="ic:baseline-star" />
            <Icon color="#FF5353" name="ic:baseline-star" />
            <Icon color="#FF5353" name="ic:baseline-star" />
            <Icon color="#FF5353" name="ic:baseline-star" />
            <span class="text-[13px] font-light ml-2">5 213 Reviews 1,000+ orders</span>
          </div>
          <div class="border-b" />
          <div class="flex items-center justify-start gap-2 my-2">
            <div class="text-xl font-bold">${{ priceComputed }}</div>
            <span class="bg-[#F5F5F5] border text-[#C08562] text-[9px] font-semibold px-1.5 rounded-sm">70% off</span>
          </div>
          <p class="text-[#009A66] text-xs font-semibold pt-1">Free 11-day delivery over $9.22</p>
          <p class="text-[#009A66] text-xs font-semibold pt-1">Free shipping</p>
          <div class="py-2" />
          <button @click="addToCart()" :disabled="isInCart"
            class="px-6 py-2 rounded-lg text-white text-lg font-semibold bg-gradient-to-r from-[#FF852A] to-[#FFAC2C]">
            <div v-if="isInCart">Is Added</div>
            <div v-else>Add to cart</div>
          </button>
        </div>
      </div>
    </div>
  </MainLayout>
</template>

<script setup>
import MainLayout from '~/layouts/MainLayout.vue'
import { useUserStore } from '~/stores/user'
const userStore = useUserStore()

const route = useRoute()

let product = ref(null)
let currentImage = ref(null)

onBeforeMount(async () => {
  product.value = await useFetch(`/api/prisma/get-product-by-id/${route.params.id}`)
})

watchEffect(() => {
  if(product.value && product.value.data) {
    currentImage.value = product.value.data.url,
    images.value[0] = product.value.data.url
    userStore.isLoading = false
  }
})

const isInCart = computed(() => {
  let res = false
  userStore.cart.forEach(prod => {
    if (route.params.id == prod.id) {
      res = true
    }
  })
  return res
})

const priceComputed = computed(() => {
  if(product.value && product.value.data) {
    return product.value.data.price / 100
  }
  return '0.00'
})


const images = ref([
  '',
  "https://shop.dupapier.com.mx/cdn/shop/products/NOTAS-ADHESIVAS-POST-IT-ULTRA-5-COLORES-654-5UC-DE-500-HOJAS_B090-037_3.jpg?v=1676323173&width=800",
  "https://th-thumbnailer.cdn-si-edu.com/k6mA8HYuWjeTgUJuORpaMYh3DS0=/800x800/https://tf-cmsv2-smithsonianmag-media.s3.amazonaws.com/filer/19/00/19000e55-a414-4fde-b790-5e5f9f4ff28b/hehuanshan-main-peak-istock-1148778056.jpg",
  "https://images.newscientist.com/wp-content/uploads/2021/12/01155751/pri211807004-2.jpg?crop=1:1,smart&width=1200&height=1200&upscale=true",
  "https://th-thumbnailer.cdn-si-edu.com/MNB-xKHAEJuM69IhGHxiO05yWdY=/800x800/https://tf-cmsv2-smithsonianmag-media.s3.amazonaws.com/filer/34/e6/34e6dcfc-5547-4ed1-815f-fc20fc359f17/gettyimages-1204446278.jpg",
  "https://www.traveloffpath.com/wp-content/uploads/2023/09/Beautiful-bay-of-Lake-Atitlan-with-view-to-Volcano-San-Pedro-in-highlands-of-Guatemala-Central-America-copy.jpg"
])

const addToCart = () => {
  userStore.cart.push(product.value.data)
}
</script>