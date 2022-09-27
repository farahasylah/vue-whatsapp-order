<template>
  <div class="md:flex md:pb-4">
    <div class="navigation md:w-1/5 w-full bg-violet-100 p-2 shadow-lg z-10">
      <div class="fixed md:h-screen relative md:pt-3 p-1 md:p-4 flex justify-around md:block md:mt-5 text-left md:text-center">
          <img src="./assets/logo.jpg" class="inline-block rounded-full shadow-xl w-24 h-24 md:w-full md:h-auto max-w-fit" >
          <div class="p-3 md:p-1">
            <h1 class="text-md md:text-xl font-bold md:mt-3">
              WeBake Store</h1>
            <h2 class="text-sm leading-4 italic mt-2 mb-4 font-medium">
              We bake things like cupcakes, cookies and custom made cakes</h2>
            <hr class="md:mb-3">
          </div>
          <div class="grid md:inline-block">
            <SocialMedia icon="Facebook" linkURL="https://www.facebook.com/"/>
            <SocialMedia icon="Instagram" linkURL="https://www.instagram.com/"/>
            <SocialMedia icon="Whatsapp" linkURL="https://api.whatsapp.com/send?phone=+60123456789"/>
          </div>
      </div>
      
    </div>
    <div class="scroll-container p-5 md:p-8 mb-4 md:w-4/5 w-full overflow-auto">
      <div v-if="currentPg === 'products'" >
        <div v-for="( item, index ) in products" :key="index" 
          class="inline-block md:flex md:flex-row bg-violet-100 border-2 border-violet-100 rounded-md drop-shadow-md text-left p-3 md:p-4 mb-3.5 w-full"
          :class="{ 'border-r-indigo-600 border-4' : checkRepeated(item)}">
          <img :src="item.img" 
            class="inline-block md:w-10 aspect-square w-2/6 float-left md:basis-1/6 object-cover rounded-lg" >
          <div class="inline-block pl-4 md:px-5 md:py-1 w-4/6 md:basis-4/6">
            <h5 class="text-lg font-bold leading-5 pb-1">
              {{ item.name }}</h5>
            <p class="font-medium">
              RM {{ item.price }}</p>
            <p v-html="item.desc" class="text-sm mt-1" ></p>
          </div>
          <div class="mt-2 px-1 md:py-4 basis-1/6 text-right">
            <small v-if="checkRepeated(item)" class="text-indigo-500" >
              Added
            </small>
            <input type="number" v-model="item.quantity" min="1" max="100"
              class="m-1.5 p-1 rounded-full text-center w-28 text-sm"/>
            <button @click="addToCart( item, index )" 
              class="m-1.5 bg-indigo-200 px-4 py-1 rounded-full text-center w-28 hover:bg-indigo-700 hover:text-white text-sm"
              :class="{ 'bg-indigo-700 text-white' : checkRepeated(item)}">
              <span v-if="!checkRepeated(item)">Add to cart</span>
              <span v-else>Update cart</span>
            </button>
          </div>
        </div>
      </div>
      <div v-if="currentPg === 'cart'" class="mb-3 md:p-5">
        <table class="cart-table table-auto w-full border-2 rounded-md shadow-lg text-center">
          <thead>
            <tr class="bg-gray-200">
              <th class="text-left">Product</th>
              <th>Price (RM)</th>
              <th>Quantity</th>
              <th>Subtotal</th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="( item, index ) in cart" :key="index" class="border-2">
              <td class="px-4 py-2 md:py-4 text-left leading-5">
                <img :src="item.img" class="inline-block basis-1/6 object-cover rounded-md mr-2 w-10 h-10" />
                {{ item.name }}
              </td>
              <td class="px-4 py-2 md:py-4">
                {{ item.price }}
              </td>
              <td class="px-4 py-2 md:py-4">
                <input type="number" v-model="item.quantity" min="1" max="100"
                  class="m-1.5 p-1 border-2 rounded-full text-center w-28 text-sm" />
              </td>
              <td class="px-4 py-2 md:py-4">
                {{ subTotal(item.price,item.quantity) }}
              </td>
              <td class="px-4 py-2 md:py-4">
                <button @click="removeFromCart(index)" hover:text-amber-900 >
                  <IconsSVG icon="close" />
                </button>
              </td>
            </tr>
            <tr>
              <th colspan="3" class="bg-gray-200 text-right" >Total</th>
              <th class="bg-gray-200 border-2 text-right md:text-center">RM {{ getTotal() }}</th>
            </tr>
          </tbody>
        </table>
        <small class="inline-block mt-2 p-1">
          *We may add in additional charge for <b>delivery</b> and <b>any special request</b> after you send this your order. Charges will be informed to customer first before payment.
        </small>
        <button @click="clearCart()" 
          class="bg-indigo-500 text-white px-4 py-1 ml-4 mt-5 rounded-full text-md hover:bg-slate-600 hover:text-white float-right">
          Clear cart
        </button>
      </div>
      <div v-if="currentPg === 'details'" class="detailpg mb-3 p-5 text-left">
        <p class="italic mb-4 text-sm">
            <IconsSVG icon="info"/> Store operating hours : 8:00 am - 8:00 pm</p>
        <h5 class="text-lg font-bold mb-2 underline">
          Order details
        </h5>
        <div class="inline-flex md:w-1/2 w-full float-left pr-2 align-center">
          <label>(Delivery / Pickup): </label>
          <select name="mode" v-model="customer[0].orderMode" 
            class="ml-1.5 py-1 px-4 rounded-full text-sm border-2 w-full" >
            <option value="delivery">Deliver to address</option>
            <option value="pickup">Pickup at store</option>
          </select>
        </div>
        <div class="inline-flex md:w-1/2 w-full">
          <div v-if="customer[0].orderMode === 'delivery'" class="pl-2 inline-flex">
            <label clas="w-1/5">Address : </label>
            <textarea v-model="customer[0].address" name="location" type="textarea" rows="3" cols="50" 
              class="ml-1.5 py-1 px-4 rounded-sm w-full text-sm border-2" 
              placeholder="Add in your delivery location"/>
          </div>
          <div v-if="customer[0].orderMode === 'pickup'" class="pl-4">
            <strong>
              <IconsSVG icon="location" /> Our store at: </strong>
              <div class="ml-7 italic"> 
                3 Jalan 5,<br>
                Taman Laman,<br>
                46000 Kuala Lumpur<br>
                <button onclick="window.open('https://goo.gl/maps/pJHjHZkRQtDQxNKt9')"
                  class="underline text-blue-700" >
                  Location map
                </button>
              </div>
          </div>
        </div>
        <small v-if="customer[0].orderMode === 'delivery'" class="italic w-full text-right inline-block">
          We will add on delivery charge based on distance</small>
        <div class="mt-2 mb-2 float-left w-full">
          <label class="capitalize">
            {{ customer[0].orderMode }} Date : 
          </label>
          <input v-model="customer[0].orderDate" name="orderDate" 
            type="datetime-local" :min="setOrderDate(new Date(),'minDate')"
            class="m-1.5 mr-2 py-1 px-4 rounded-full text-sm border-2"/>
          <span>
            {{ setOrderDate(customer[0].orderDate,'day') }}
          </span>
        </div>
        <h5 class="text-lg font-bold mt-10 mb-2 w-full inline-block underline">
          Personal details
        </h5>
          <div class="inline-flex md:w-1/2 w-full pr-2 align-center">
            <label>Name : </label>
            <input v-model="customer[0].fullName" 
              name="fullName" type="text" 
              class="ml-2 py-1 px-4 rounded-full text-sm border-2 w-full"/>
          </div>
          <div class="inline-flex md:w-1/2 w-full pt-3 md:pl-2 align-center">
            <label>Phone number : </label>
            <input v-model="customer[0].phoneNumber" 
              name="phoneNumber" type="text" 
              class="ml-2 py-1 px-4 rounded-full text-sm border-2 w-full"/>
          </div>
        <div class="inline-flex w-full mt-3">
          <label clas="w-1/5">Additonal Request : </label>
          <textarea v-model="customer[0].addRequest" 
            name="addRequest" type="textarea" rows="3" cols="50" 
            class="ml-1.5 py-1 px-4 rounded-sm w-full text-sm border-2"/>
        </div>
      </div>
      <div v-if="currentPg === 'confirm'" >
        <table class="table-auto w-full border-2 rounded-md shadow-lg text-center">
          <thead>
            <tr class="bg-gray-200">
              <th class="text-left">Product</th>
              <th class="hidden md:table-cell">Price (RM)</th>
              <th>Quantity</th>
              <th>Subtotal</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="( item, index ) in cart" :key="index" class="border-2">
              <td class="px-4 py-4 text-left leading-5">
                <img :src="item.img"
                  class="block md:inline-block basis-1/6 object-cover rounded-md mr-2 w-10 h-10" />
                  {{ item.name }}</td>
              <td class="px-2 py-4 hidden md:table-cell">{{ item.price }}</td>
              <td class="px-2 py-4" ><span md:hidden table-cell>RM {{ item.price }} x </span>{{ item.quantity }}</td>
              <td class="px-2 py-4">{{ subTotal(item.price,item.quantity) }}</td>
            </tr>
            <tr>
              <th class="bg-gray-200 hidden md:table-cell"></th>
              <th colspan="2" class="bg-gray-200 text-right">Total</th>
              <th class="bg-gray-200 border-2">RM {{ getTotal() }}</th>
            </tr>
          </tbody>
        </table>
        <small class="inline-block mt-2 p-1">
          *We may add in additional charge for <b>delivery</b> and <b>any special request</b> after you send this your order. Charges will be informed to customer first before payment.
        </small>
        <table class="table-auto w-full border-2 rounded-md shadow-lg text-left mt-5">
          <tr>
            <th>Name:</th>
            <td>{{ customer[0].fullName }}</td>
          </tr>
          <tr>
            <th>Phone number:</th>
            <td>{{ customer[0].phoneNumber }}</td>
          </tr>
          <tr>
            <th class="capitalize">{{ customer[0].orderMode }} Date &amp; Time:</th>
            <td>{{ setOrderDate(customer[0].orderDate) }}</td>
          </tr>
          <tr v-if="customer[0].orderMode === 'delivery'">
            <th>Address:</th>
            <td>{{ customer[0].address }}</td>
          </tr>
          <tr v-if="customer[0].addRequest != ''">
            <th>Additional request:</th>
            <td>{{ customer[0].addRequest }}</td>
          </tr>
        </table>
      </div>
    </div>
    <div class="cart fixed bottom-0 right-0 bg-violet-300 p-2 md:p-3 w-full shadow-xl text-sm place-content-end">
        <button v-if="currentPg === 'cart' && cart.length > 0" 
          class="bg-violet-800 text-white px-4 py-1 ml-3 rounded-full text-md hover:bg-indigo-900 mr-5" 
          @click="currentPage = 'details'">
          Next
        </button>
        <button v-if="currentPg === 'products' && cart.length > 0" 
          class="bg-violet-800 text-white px-4 py-1 ml-3 rounded-full text-md hover:bg-indigo-900 mr-5" 
          @click="changePg('cart')">
          View cart
        </button>
        <button v-if="allHasFilled && currentPg === 'details'" 
          class="bg-violet-800 text-white px-4 py-1 ml-3 rounded-full text-md hover:bg-indigo-900 mr-5" 
          @click="changePg('confirm')">
          Review order
        </button>
        <button v-if="currentPg === 'confirm'" 
          class="bg-green-700 text-white px-4 py-1 ml-3 mr-5 rounded-full text-md hover:bg-green-900" 
          @click="orderWhatsapp()">
          <IconsSVG icon="Whatsapp"/>
          Send order
        </button>
        <button v-if="currentPg != 'products'" 
          class="bg-indigo-500 text-white px-4 py-1 ml-3 rounded-full text-md hover:bg-slate-600 hover:text-white mr-5" 
          @click="changePg('products')" >
          <span>Back to products</span>
        </button>
        <span v-if="currentPg === 'products'" class="m-1">
          {{ cart.length }} products added
        </span>
      </div>
  </div>
</template>

<script>
import './assets/index.css'
import SocialMedia from './components/SocialMedia.vue'
import IconsSVG from './components/IconsSVG.vue'
export default {
  name: 'App',
  mounted(){
    document.title = 'Create Whatsapp order with Vue'
  },
  data() {
    return{
      products: [
        { name: 'Fruit tart', price: 12, quantity: 1, 
          desc: 'A stunning and delicious tart made with buttery crust and cream filling, topped with colorful berries', 
          img: 'https://images.pexels.com/photos/461431/pexels-photo-461431.jpeg'},
        { name: 'Vanilla cupcake with Buttercream icing', price: 5, quantity: 1, 
          desc: '1 piece<br>Sweet, fluffy vanilla cupcake topped with vanilla buttercream', 
          img: 'https://images.pexels.com/photos/1179002/pexels-photo-1179002.jpeg' },
        { name: 'Chocolate cupcake', price: 5, quantity: 1, 
          desc: '1 piece<br>Super moist and bursting with rich, chocolaty flavor.', 
          img:'https://images.pexels.com/photos/4693168/pexels-photo-4693168.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=2' },
        { name: 'Chocolate Chip Cookies', price: 8, quantity: 1, 
          desc: '1 piece<br>Classic chocolate chip cookies with crisp edges and soft centers', 
          img: 'https://images.pexels.com/photos/7243524/pexels-photo-7243524.jpeg?auto=compress&cs=tinysrgb&w=600'},
        { name: 'Mountain Mud Cake', price: 35, quantity: 1, 
          desc: 'Ultimate celebration cake starring different-sized Toblerone bars', 
          img:'https://images.pexels.com/photos/11663130/pexels-photo-11663130.jpeg?auto=compress&cs=tinysrgb&w=600&lazy=load' },
      ],
      cart: [],
      currentPg: 'products',
      customer: [{
        fullName: '',
        phoneNumber: '',
        orderMode: '',
        address: '',
        orderDate: '',
        addRequest: '',
      }],
      allFilled: false,
    }
  },
  methods :{
    addToCart( product, index ){ 
      if( this.checkRepeated(product) ){
        this.cart.splice(index,1,product);
      }
      else { 
        this.cart.push(product); }
    },
    removeFromCart(index){
      this.cart.splice(index,1);
      if( this.cart.length === 0 ){
        this.currentPg = 'products';}
    },
    clearCart(){
      this.cart.splice(0);
      this.currentPg = 'products';
    },
    changePg(page){
      this.currentPg = page;
    },
    checkRepeated(product){
      let repeated = this.cart.some(e => e === product);
      return repeated;
    },
    setOrderDate(date,format){
      if( new Date(date) != 'Invalid Date' ){
        let time = new Date(date).toLocaleTimeString('en-us');
        if( format === 'day' ){
          return new Date(date).toLocaleDateString('en-us', { weekday:"long" });
        } else if( format === 'minDate' ){
          let addDays = new Date(date);
          addDays.setDate(addDays.getDate() + 1);
          return addDays.toISOString().split('T')[0] +'T00:00';
        } else {
          return new Date(date).toLocaleDateString('en-us', { weekday:"long", year:"numeric", month:"short", day:"numeric"}) + '-' + time;
        }
      }
    },
    subTotal( price, quantity){
      return parseFloat(price * quantity).toFixed(2);
    },
    getTotal(){
      let total = 0;
      this.cart.forEach((item) => {
        total += (item.price * item.quantity);
      })
      return parseFloat(total).toFixed(2);
    },
    orderWhatsapp(){
      let phone = '+60177160734';
      let customer = this.customer[0];
      let string = '';
      let items = 'NEXT';
      let orderMode = (customer.orderMode).toUpperCase();
      let link = 'https://api.whatsapp.com/send?phone=' + phone + '&text=';
      let header = '*ORDER FORM : ' +orderMode+ '*' + 'NEXTNEXT' 
        + 'Order Date:  *' + this.setOrderDate(customer.orderDate) + '*NEXTNEXT' + '_Order_';
      let total = '*Total : RM ' + this.getTotal() + '*';
      let details = 'NEXTNEXT' + '_Details_' + 'NEXT' 
        + 'Name:  ' +  customer.fullName 
        + 'NEXT' + 'Phone number:  ' + customer.phoneNumber;
      if( customer.address != ''){
        details += 'NEXT' + 'Address: NEXT' + customer.address;
      }
      let request = '';
      if( customer.addRequest ){
        request = 'NEXTNEXT' + '_Request_' + 'NEXT' + customer.addRequest;
      }
      this.cart.forEach((item) => {
        items += '- ' + item.name + '  X  ' + item.quantity + ' = RM ' 
        + this.subTotal(item.price,item.quantity) + 'NEXT';
      });
      string = encodeURI(header + items + total + details + request);
      string = string.split('NEXT').join('%0A');
      link = link + string;
      window.open(link);
    },
  },
  computed: {
    orderMode:{
      get: function(){
       return this.customer[0].orderMode;
      },
      set: function(mode){
       this.customer[0].orderMode = mode;
      },
    },
    currentPage: {
      get: function(){
       return this.currentPg;
      },
      set: function(current){
       this.currentPg = current;
      },
    },
    allHasFilled: {
      get: function(){
        let customer = this.customer[0];
        if( customer.fullName != '' && customer.phoneNumber != '' && customer.orderMode != '' && customer.orderDate != ''){
          if( customer.orderMode === 'pickup' ){
            return true;
          } else if( customer.orderMode === 'delivery' && customer.address != '' ){
            return true;
          } else{ return false; } 
        }
        return false;
      }
    }
  },
  components: { SocialMedia, IconsSVG},
}
</script>
