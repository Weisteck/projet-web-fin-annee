<template>

  <div class="text-left">
    <div class="container mx-auto">
      <div class="grid grid-cols-12 mb-10">
        <div class="col-span-12 lg:col-span-7 md:col-span-6 sm:col-span-12">
          <div class="grid grid-cols-12 col-span-12 mb-10">
            <div class="col-span-2 lg:col-span-2 md:col-span-6 sm:col-span-12">
              <div v-for="image in product.images">
                <img :src="image" alt="productImage"
                     class="w-full h-32 object-cover cursor-pointer transition border hover:border-gray-400 border-1 hover:border-8 border-white"
                     @click="changeImageToShow(image)">
              </div>
            </div>
            <div class="col-span-10 lg:col-span-10 md:col-span-6 sm:col-span-12">
              <img :src="imageToShow" alt="productImage" class="object-cover h-full w-full border border-white">
            </div>
          </div>
        </div>
        <div class="col-span-12 lg:col-span-5 md:col-span-6 sm:col-span-12 text-center">


          <button class="btn-primary" @click="deleteLocalStorage">
            delete localstorage
          </button>


          <img :src="product.brand.logo" alt="brandLogo" class="mx-auto" height="200" width="200">
          <h1 class="title">
            {{ product.brand.name }} - {{ product.model }} fdsdfds
          </h1>
          <div class="flex justify-center m-10">
            <div v-for="n in product.rating">
              <div class="mx-auto">⭐</div>
            </div>
          </div>

          <p class="text-sm">
            {{ fixPriceHt(product.priceExclTax, 0) }} HT
          </p>
          <p class="text-xl">
            {{ fixPriceTtc(product.priceExclTax, 0) }} TTC
          </p>

          <div class="grid grid-cols-12">
            <div class="col-span-2">
              <p>Couleur</p>
            </div>
            <div class="col-span-10">
              <div class="flex mt-3 mb-3 ml-5 justify-center">
                <div v-for="variant in product.variants">
                  <div class="flex-initial mr-2 p-3 rounded-xl border" :class="`bg-${variant.color.toLowerCase()}-300`">
                    <input v-model="variantSelected" :value="variant" type="radio" :id="variant.color"
                           class="text-gray-600 bg-red-700 text-red-500"
                           name="color">
                  </div>
                </div>
              </div>
            </div>

            <div class="col-span-2">
              <p>Taille</p>
            </div>
            <div class="col-span-10">
              <div class="flex mt-3 mb-3 mx-auto w-1/2">
                <template v-for="variant in product.variants">
                  <template v-for="size in variant.sizes">
                    <button class="button mr-3" @click="sizeSelected = size.size" :disabled="size.stock <= 0">
                      <span>{{ size.size }}</span>
                    </button>
                  </template>
                </template>
              </div>
            </div>
          </div>


          <br>

          <button @click="addProductToCart" class="button" :disabled="colorAndSizeNotSelected">
            AJOUTER AU PANIER
          </button>
        </div>
      </div>

      <hr class="mb-10">

      <div class="grid grid-cols-12">
        <div class="col-span-3">
          <h2 class="text-xl mb-3">Description</h2>
          <p class="description">
            {{ product.details.description }}
          </p>
        </div>

        <div class="col-span-3">
          <p class="text-lg mb-3">Catégories</p>
          <div class="flex">
            <div v-for="category in product.categories" class="flex">
              <div class="flex-initial mr-2 bg-gray-200 p-1.5 rounded-xl">
                <span>{{ category }}</span>
              </div>
            </div>
          </div>
        </div>

        <div class="col-span-3">
          <p class="text-lg mb-3">Matériaux</p>
          <div class="flex">
            <div v-for="materiel in product.details.materials" class="flex">
              <div class="flex-initial mr-2 bg-gray-200 p-1.5 rounded-xl">
                <span>{{ materiel }}</span>
              </div>
            </div>
          </div>
        </div>

        <div class="col-span-3">
          <p class="text-lg mb-3">Origine</p>
          <p>{{ product.details.origin }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DetailProduct',
  data() {
    return {
      product: {
        categories: [],
        brand: {
          name: "",
          brandDescription: "",
          logo: ""
        },
        model: "",
        details: {
          description: "",
          materials: [],
          origin: ""
        },
        rating: 0,
        priceExclTax: 0,
        images: [],
        variants: [
          {
            color: "",
            sizes: [
              {
                size: 0,
                stock: 0
              }
            ]
          }
        ]
      },
      noImage: "https://upload.wikimedia.org/wikipedia/commons/thumb/a/ac/No_image_available.svg/768px-No_image_available.svg.png",
      colorAndSizeNotSelected: true,
      variantSelected: "",
      sizeSelected: 0,
      imageToShow: "",
    }
  },
  watch: {
    variantSelected() {
      this.sizeSelected = 0
    },
    sizeSelected(val) {
      val !== 0 ? this.colorAndSizeNotSelected = false : this.colorAndSizeNotSelected = true
    }
  },
  methods: {
    deleteLocalStorage() {
      localStorage.removeItem('cartId')
    },

    changeImageToShow(image) {
      this.imageToShow = image
    },

    getProduct() {
      this.$store.dispatch('getProduct', { id: this.$route.params.id })
        .then(res => {
          this.product = res.data
          this.imageToShow = res.data.images[0]
        })
        .catch(err => console.error(err))
    },

    fixPriceHt(priceHt, priceDelivery) {
      priceHt += priceDelivery
      return priceHt.toString().substring(0, priceHt.toString().length - 2)
        + "."
        + priceHt.toString().slice(-2)
        + " €"
    },

    fixPriceTtc(priceHt, priceDelivery) {
      priceHt += priceDelivery
      return (Number(
          priceHt.toString().substring(0, priceHt.toString().length - 2)
          + '.'
          + priceHt.toString().slice(-2)
          ) * 1.20
        ).toFixed(2)
        + " €"
    },

    addProductToCart() {
      const selectedProduct = {
        productId: this.product._id,
        brand: this.product.brand.name,
        model: this.product.model,
        price: this.product.priceExclTax,
        image: this.product.images[0],
        color: this.variantSelected.color,
        size: this.sizeSelected
      }

      if (localStorage.getItem('cartId'))
        this.$store.dispatch('addProductToCart', {
          cartId: localStorage.getItem('cartId'),
          productSelected: selectedProduct
        })
          .then(res => {
          })
          .catch(err => console.error(err))
      else
        this.$store.dispatch('addProductToNewCart', selectedProduct)
          .then(res => {

            // TODO: Si l'utilisateur n'est pas connecté, alors stocker l'id du panier créer dans le local storage
            localStorage.setItem('cartId', res.data._id)
          })
          .catch(err => console.error(err))
    }
  },
  beforeMount() {
    this.getProduct()
  }
};
</script>
