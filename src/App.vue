<!-- The exported code uses Tailwind CSS. Install Tailwind CSS in your dev environment to ensure all styles work. -->
<template>
  <div class="min-h-screen bg-gray-50">
    <!-- POS Profile Selection Modal -->
    <div v-if="showPosProfileModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
      <div class="bg-white rounded-lg shadow-xl w-96 max-h-[90vh] overflow-y-auto">
        <div class="p-6">
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-xl font-bold text-gray-800">Select POS Profile</h2>
          </div>
          <div class="mb-6">
            <div class="mb-4">
              <label class="block text-gray-700 font-medium mb-2">POS Profile</label>
              <div class="relative">
                <button @click="togglePosProfileDropdown"
                  class="w-full px-3 py-2 bg-gray-50 rounded-md text-left text-sm flex items-center justify-between border border-gray-200 !rounded-button">
                  <span>{{ selectedPosProfile ? selectedPosProfile.name : 'Select POS Profile' }}</span>
                  <i class="fas fa-chevron-down text-gray-400"></i>
                </button>
                <div v-if="showPosProfileDropdown"
                  class="absolute z-10 w-full mt-1 bg-white rounded-md shadow-lg border border-gray-200 max-h-48 overflow-y-auto">
                  <div v-for="profile in posProfiles" :key="profile.id" @click="selectPosProfile(profile)"
                    class="px-3 py-2 hover:bg-gray-50 cursor-pointer text-sm">
                    {{ profile.name }}
                  </div>
                </div>
              </div>
            </div>
            <div class="mb-4">
              <label class="block text-gray-700 font-medium mb-2">POS Name</label>
              <input type="text" v-model="posName" placeholder="Enter POS Name"
                class="w-full px-3 py-2 rounded-md border-none bg-gray-100 text-sm focus:ring-2 focus:ring-indigo-500">
            </div>
          </div>
          <div class="flex space-x-3">
            <button @click="confirmPosProfile" :disabled="!canConfirmPosProfile" :class="[
              'flex-1 py-3 rounded-md font-medium cursor-pointer !rounded-button whitespace-nowrap',
              canConfirmPosProfile ? 'bg-indigo-600 text-white hover:bg-indigo-700' : 'bg-gray-300 text-gray-500 cursor-not-allowed'
            ]">
              Confirm
            </button>
          </div>
        </div>
      </div>
    </div>
    <!-- SEO Meta Tags -->
    <div v-if="false">
      <meta name="description"
        content="FoodiePoint POS - Advanced Restaurant Management System. Order management, table booking, and payment processing made easy.">
      <meta name="keywords" content="restaurant pos, food ordering, table management, digital menu, payment system">
      <meta name="author" content="FoodiePoint">
      <meta property="og:title" content="FoodiePoint POS System">
      <meta property="og:description" content="Advanced Restaurant Management System">
      <meta property="og:type" content="website">
      <meta name="twitter:card" content="summary_large_image">
      <meta name="twitter:title" content="FoodiePoint POS System">
      <meta name="twitter:description" content="Advanced Restaurant Management System">
    </div>
    <!-- Top Navigation Bar -->
    <header class="bg-white shadow-md">
      <div class="container mx-auto px-4 py-3 flex flex-col md:flex-row items-center justify-between gap-3">
        <div class="flex items-center w-full md:w-auto justify-between">
          <h1 class="text-xl md:text-2xl font-bold text-indigo-700">FoodiePoint POS</h1>
          <button class="md:hidden text-gray-600" @click="toggleMobileMenu">
            <i class="fas fa-bars text-xl"></i>
          </button>
        </div>
        <div class="flex flex-col md:flex-row items-center space-y-2 md:space-y-0 md:space-x-6 w-full md:w-auto"
          :class="{ 'hidden': !showMobileMenu, 'md:flex': true }">
          <div class="text-gray-600">
            <span class="font-medium">{{ currentDate }}</span> |
            <span>{{ currentTime }}</span>
          </div>
          <div class="flex items-center">
            <i class="fas fa-user-circle text-indigo-600 text-xl mr-2"></i>
            <span class="font-medium text-gray-700">John Smith</span>
          </div>
          <div class="flex space-x-4">
            <button class="text-gray-600 hover:text-indigo-600 cursor-pointer">
              <i class="fas fa-home text-lg"></i>
            </button>
            <button class="text-gray-600 hover:text-indigo-600 cursor-pointer">
              <i class="fas fa-cog text-lg"></i>
            </button>
            <button class="text-gray-600 hover:text-red-600 cursor-pointer">
              <i class="fas fa-sign-out-alt text-lg"></i>
            </button>
          </div>
        </div>
      </div>
    </header>
    <div class="container mx-auto px-4 py-6">
      <!-- Main Content Area -->
      <div class="flex flex-col space-y-6">
        <!-- Room Selection Tabs -->
        <div class="bg-white rounded-lg shadow-md p-4">
          <h2 class="text-lg font-semibold text-gray-800 mb-3">Select Room</h2>
          <div class="flex space-x-2 overflow-x-auto pb-2">
            <button v-for="(room, index) in rooms" :key="index" @click="selectRoom(room)" :class="[
              'px-4 py-2 rounded-md font-medium whitespace-nowrap cursor-pointer !rounded-button',
              selectedRoom === room ? 'bg-indigo-600 text-white' : 'bg-gray-100 text-gray-700 hover:bg-gray-200'
            ]">
              {{ room.name }}
            </button>
          </div>
        </div>
        <!-- Table Selection Grid -->
        <div class="bg-white rounded-lg shadow-md p-4">
          <div class="flex justify-between items-center mb-4">
            <h2 class="text-lg font-semibold text-gray-800">
              Tables in {{ selectedRoom.name }}
            </h2>
            <div class="flex items-center space-x-4">
              <div class="flex items-center">
                <div class="w-3 h-3 rounded-full bg-green-500 mr-1"></div>
                <span class="text-sm text-gray-600">Available</span>
              </div>
              <div class="flex items-center">
                <div class="w-3 h-3 rounded-full bg-red-500 mr-1"></div>
                <span class="text-sm text-gray-600">Occupied</span>
              </div>
              <div class="flex items-center">
                <div class="w-3 h-3 rounded-full bg-yellow-500 mr-1"></div>
                <span class="text-sm text-gray-600">Reserved</span>
              </div>
            </div>
          </div>
          <div class="overflow-x-auto pb-2">
            <div class="flex gap-3 min-w-max pr-2">
              <div v-for="table in selectedRoom.tables" :key="table.id" @click="selectTable(table)" :class="[
                'p-3 rounded-lg border-2 flex flex-col items-center justify-center w-24 h-24 cursor-pointer transition-all shrink-0',
                getTableStatusClass(table),
                selectedTable?.id === table.id ? 'ring-2 ring-offset-2 ring-indigo-500' : ''
              ]">
                <i class="fas fa-utensils text-lg mb-1"></i>
                <span class="font-bold text-sm">T{{ table.number }}</span>
                <span class="text-xs">{{ table.seats }} seats</span>
              </div>
            </div>
          </div>
        </div>
        <!-- Order Processing Area -->
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
          <!-- Menu Categories and Items -->
          <div class="lg:col-span-2 bg-white rounded-lg shadow-md">
            <div class="flex flex-col sm:flex-row h-full">
              <!-- Categories Sidebar -->
              <div class="w-full sm:w-1/4 border-b sm:border-b-0 sm:border-r border-gray-200 p-4">
                <h3 class="font-semibold text-gray-700 mb-4">Categories</h3>
                <div class="space-y-2">
                  <button v-for="(category, index) in menuCategories" :key="index" @click="selectCategory(category)"
                    :class="[
                      'w-full text-left px-3 py-2 rounded-md cursor-pointer whitespace-nowrap !rounded-button',
                      selectedCategory === category ? 'bg-indigo-100 text-indigo-700 font-medium' : 'text-gray-600 hover:bg-gray-100'
                    ]">
                    <i :class="['mr-2', category.icon]"></i>
                    {{ category.name }}
                  </button>
                </div>
              </div>
              <!-- Menu Items Grid -->
              <div class="w-full sm:w-3/4 p-4">
                <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-3 mb-4">
                  <h3 class="font-semibold text-gray-700">{{ selectedCategory.name }}</h3>
                  <div class="flex items-center space-x-3">
                    <div class="relative">
                      <input type="text" placeholder="Search items..."
                        class="pl-8 pr-4 py-2 rounded-md border-none bg-gray-100 text-sm focus:ring-2 focus:ring-indigo-500"
                        v-model="searchQuery">
                      <i class="fas fa-search absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400"></i>
                    </div>
                    <div class="flex space-x-2">
                      <button @click="viewMode = 'grid'" :class="[
                        'p-2 rounded-md cursor-pointer !rounded-button whitespace-nowrap',
                        viewMode === 'grid' ? 'bg-indigo-100 text-indigo-600' : 'bg-gray-100 text-gray-600'
                      ]">
                        <i class="fas fa-th-large"></i>
                      </button>
                      <button @click="viewMode = 'list'" :class="[
                        'p-2 rounded-md cursor-pointer !rounded-button whitespace-nowrap',
                        viewMode === 'list' ? 'bg-indigo-100 text-indigo-600' : 'bg-gray-100 text-gray-600'
                      ]">
                        <i class="fas fa-list"></i>
                      </button>
                    </div>
                  </div>
                </div>
                <!-- Grid View -->
                <div v-if="viewMode === 'grid'"
                  class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-3 overflow-y-auto max-h-[500px]">
                  <div v-for="item in filteredMenuItems" :key="item.id"
                    class="border border-gray-200 rounded-lg overflow-hidden hover:shadow-md transition-shadow cursor-pointer"
                    @click="addItemToOrder(item)">
                    <div class="h-32 overflow-hidden">
                      <img :src="item.image" alt="item.name" class="w-full h-full object-cover object-top">
                    </div>
                    <div class="p-3">
                      <div class="flex justify-between items-start">
                        <h4 class="font-medium text-gray-800">{{ item.name }}</h4>
                        <span class="font-bold text-indigo-600">${{ item.price.toFixed(2) }}</span>
                      </div>
                      <div class="mt-2 flex justify-between items-center">
                        <span class="text-xs text-gray-500">{{ item.description }}</span>
                        <button
                          class="text-indigo-600 hover:text-indigo-800 cursor-pointer !rounded-button whitespace-nowrap">
                          <i class="fas fa-plus-circle"></i>
                        </button>
                      </div>
                    </div>
                  </div>
                </div>
                <!-- List View -->
                <div v-else class="overflow-y-auto max-h-[500px]">
                  <div v-for="item in filteredMenuItems" :key="item.id"
                    class="border border-gray-200 rounded-lg p-3 mb-2 hover:bg-gray-50 transition-colors cursor-pointer flex items-center"
                    @click="addItemToOrder(item)">
                    <div class="h-16 w-16 overflow-hidden rounded-md mr-3 flex-shrink-0">
                      <img :src="item.image" alt="item.name" class="w-full h-full object-cover object-top">
                    </div>
                    <div class="flex-grow">
                      <div class="flex justify-between items-center">
                        <h4 class="font-medium text-gray-800">{{ item.name }}</h4>
                        <span class="font-bold text-indigo-600">${{ item.price.toFixed(2) }}</span>
                      </div>
                      <p class="text-xs text-gray-500 mt-1">{{ item.description }}</p>
                    </div>
                    <button
                      class="ml-3 text-indigo-600 hover:text-indigo-800 cursor-pointer !rounded-button whitespace-nowrap">
                      <i class="fas fa-plus-circle text-lg"></i>
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <!-- Order Summary and Actions -->
          <div class="col-span-1 bg-white rounded-lg shadow-md p-4 flex flex-col">
            <div class="border-b pb-3 mb-3">
              <div class="flex justify-between items-center mb-3">
                <h3 class="font-semibold text-gray-800">Current Order</h3>
                <span v-if="selectedTable" class="bg-indigo-100 text-indigo-700 px-2 py-1 rounded text-sm font-medium">
                  Table {{ selectedTable.number }}
                </span>
                <span v-else class="bg-gray-100 text-gray-500 px-2 py-1 rounded text-sm">
                  No table selected
                </span>
              </div>
              <!-- Customer and Waiter Selection -->
              <div class="space-y-2">
                <div class="flex items-center space-x-2">
                  <div class="relative flex-1">
                    <button @click="showCustomerDropdown = !showCustomerDropdown"
                      class="w-full px-3 py-2 bg-gray-50 rounded-md text-left text-sm flex items-center justify-between border border-gray-200 !rounded-button">
                      <span>{{ selectedCustomer ? selectedCustomer.name : 'Select Customer' }}</span>
                      <i class="fas fa-chevron-down text-gray-400"></i>
                    </button>
                    <div v-if="showCustomerDropdown"
                      class="absolute z-10 w-full mt-1 bg-white rounded-md shadow-lg border border-gray-200 max-h-48 overflow-y-auto">
                      <div v-for="customer in customers" :key="customer.id" @click="selectCustomer(customer)"
                        class="px-3 py-2 hover:bg-gray-50 cursor-pointer text-sm">
                        {{ customer.name }}
                      </div>
                    </div>
                  </div>
                  <button @click="showAddCustomerModal = true"
                    class="px-3 py-2 bg-gray-100 text-gray-600 rounded-md hover:bg-gray-200 !rounded-button whitespace-nowrap">
                    <i class="fas fa-plus"></i>
                  </button>
                </div>
                <div class="flex items-center space-x-2">
                  <div class="relative flex-1">
                    <button @click="showWaiterDropdown = !showWaiterDropdown"
                      class="w-full px-3 py-2 bg-gray-50 rounded-md text-left text-sm flex items-center justify-between border border-gray-200 !rounded-button">
                      <span>{{ selectedWaiter ? selectedWaiter.name : 'Select Waiter' }}</span>
                      <i class="fas fa-chevron-down text-gray-400"></i>
                    </button>
                    <div v-if="showWaiterDropdown"
                      class="absolute z-10 w-full mt-1 bg-white rounded-md shadow-lg border border-gray-200 max-h-48 overflow-y-auto">
                      <div v-for="waiter in waiters" :key="waiter.id" @click="selectWaiter(waiter)"
                        class="px-3 py-2 hover:bg-gray-50 cursor-pointer text-sm">
                        {{ waiter.name }}
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <div class="flex-grow overflow-y-auto mb-4 max-h-[400px]">
              <div v-if="currentOrder.items.length === 0"
                class="flex flex-col items-center justify-center h-40 text-gray-400">
                <i class="fas fa-shopping-cart text-4xl mb-2"></i>
                <p>Your order is empty</p>
              </div>
              <div v-else class="space-y-3 pr-2">
                <div v-for="(item, index) in currentOrder.items" :key="index"
                  class="flex justify-between items-center p-2 hover:bg-gray-50 rounded-md">
                  <div class="flex-grow">
                    <h4 class="font-medium text-gray-800">{{ item.name }}</h4>
                    <p class="text-sm text-gray-500">${{ item.price.toFixed(2) }}</p>
                  </div>
                  <div class="flex items-center space-x-2">
                    <button @click="decreaseItemQuantity(index)"
                      class="w-7 h-7 flex items-center justify-center rounded-full bg-gray-200 text-gray-700 hover:bg-gray-300 cursor-pointer !rounded-button whitespace-nowrap">
                      <i class="fas fa-minus text-xs"></i>
                    </button>
                    <span class="w-8 text-center">{{ item.quantity }}</span>
                    <button @click="increaseItemQuantity(index)"
                      class="w-7 h-7 flex items-center justify-center rounded-full bg-gray-200 text-gray-700 hover:bg-gray-300 cursor-pointer !rounded-button whitespace-nowrap">
                      <i class="fas fa-plus text-xs"></i>
                    </button>
                    <button @click="removeItemFromOrder(index)"
                      class="w-7 h-7 flex items-center justify-center rounded-full bg-red-100 text-red-600 hover:bg-red-200 cursor-pointer !rounded-button whitespace-nowrap">
                      <i class="fas fa-trash-alt text-xs"></i>
                    </button>
                  </div>
                </div>
              </div>
            </div>
            <div class="border-t pt-3">
              <div class="space-y-2 mb-4">
                <div class="flex justify-between">
                  <span class="text-gray-600">Subtotal</span>
                  <span class="font-medium">${{ calculateSubtotal().toFixed(2) }}</span>
                </div>
                <div class="flex justify-between">
                  <span class="text-gray-600">Tax (10%)</span>
                  <span>${{ calculateTax().toFixed(2) }}</span>
                </div>
                <div class="flex justify-between text-lg font-bold">
                  <span>Total</span>
                  <span>${{ calculateTotal().toFixed(2) }}</span>
                </div>
              </div>
              <div class="space-y-3 mt-6">
                <button @click="printKOT" :disabled="!canPlaceOrder" :class="[
                  'w-full py-2 rounded-md font-medium flex items-center justify-center space-x-2 cursor-pointer !rounded-button whitespace-nowrap',
                  canPlaceOrder ? 'bg-green-600 text-white hover:bg-green-700' : 'bg-gray-300 text-gray-500 cursor-not-allowed'
                ]">
                  <i class="fas fa-print"></i>
                  <span>Print KOT</span>
                </button>
                <button @click="proceedToPayment" :disabled="!canPlaceOrder" :class="[
                  'w-full py-2 rounded-md font-medium flex items-center justify-center space-x-2 cursor-pointer !rounded-button whitespace-nowrap',
                  canPlaceOrder ? 'bg-indigo-600 text-white hover:bg-indigo-700' : 'bg-gray-300 text-gray-500 cursor-not-allowed'
                ]">
                  <i class="fas fa-credit-card"></i>
                  <span>Proceed to Payment</span>
                </button>
                <button @click="clearOrder" :disabled="currentOrder.items.length === 0" :class="[
                  'w-full py-2 rounded-md font-medium flex items-center justify-center space-x-2 cursor-pointer !rounded-button whitespace-nowrap',
                  currentOrder.items.length > 0 ? 'bg-red-100 text-red-600 hover:bg-red-200' : 'bg-gray-100 text-gray-400 cursor-not-allowed'
                ]">
                  <i class="fas fa-times"></i>
                  <span>Cancel Order</span>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- Payment Modal -->
    <div v-if="showPaymentModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
      <div class="bg-white rounded-lg shadow-xl w-1/3 max-h-[90vh] overflow-y-auto">
        <div class="p-6">
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-xl font-bold text-gray-800">Payment</h2>
            <button @click="showPaymentModal = false" class="text-gray-500 hover:text-gray-700 cursor-pointer">
              <i class="fas fa-times"></i>
            </button>
          </div>
          <div class="mb-6">
            <div class="bg-indigo-50 p-4 rounded-lg mb-4">
              <div class="flex justify-between mb-2">
                <span class="text-gray-600">Table</span>
                <span class="font-medium">{{ selectedTable?.number }}</span>
              </div>
              <div class="flex justify-between mb-2">
                <span class="text-gray-600">Items</span>
                <span class="font-medium">{{ currentOrder.items.length }}</span>
              </div>
              <div class="flex justify-between mb-2">
                <span class="text-gray-600">Subtotal</span>
                <span class="font-medium">${{ calculateSubtotal().toFixed(2) }}</span>
              </div>
              <div class="flex justify-between mb-2">
                <span class="text-gray-600">Tax (10%)</span>
                <span>${{ calculateTax().toFixed(2) }}</span>
              </div>
              <div class="flex justify-between text-lg font-bold">
                <span>Total</span>
                <span>${{ calculateTotal().toFixed(2) }}</span>
              </div>
            </div>
            <div class="mb-4">
              <label class="block text-gray-700 font-medium mb-2">Payment Method</label>
              <div class="grid grid-cols-3 gap-3">
                <button v-for="method in paymentMethods" :key="method.id" @click="selectedPaymentMethod = method.id"
                  :class="[
                    'py-3 px-4 border rounded-md flex flex-col items-center justify-center cursor-pointer !rounded-button whitespace-nowrap',
                    selectedPaymentMethod === method.id ? 'border-indigo-500 bg-indigo-50' : 'border-gray-300 hover:bg-gray-50'
                  ]">
                  <i
                    :class="['text-xl mb-1', method.icon, selectedPaymentMethod === method.id ? 'text-indigo-600' : 'text-gray-600']"></i>
                  <span :class="selectedPaymentMethod === method.id ? 'text-indigo-600' : 'text-gray-600'">{{
                    method.name }}</span>
                </button>
              </div>
            </div>
            <div class="mb-4">
              <label class="block text-gray-700 font-medium mb-2">Discount</label>
              <div class="flex">
                <input type="number"
                  class="w-full rounded-l-md border-none bg-gray-100 text-sm focus:ring-2 focus:ring-indigo-500"
                  placeholder="Enter discount amount" v-model="discountAmount">
                <button
                  class="bg-gray-200 px-4 rounded-r-md text-gray-700 !rounded-button whitespace-nowrap">Apply</button>
              </div>
            </div>
          </div>
          <div class="flex space-x-3">
            <button @click="processPayment"
              class="flex-1 bg-green-600 text-white py-3 rounded-md font-medium hover:bg-green-700 cursor-pointer !rounded-button whitespace-nowrap">
              Complete Payment
            </button>
            <button @click="showPaymentModal = false"
              class="flex-1 bg-gray-200 text-gray-700 py-3 rounded-md font-medium hover:bg-gray-300 cursor-pointer !rounded-button whitespace-nowrap">
              Cancel
            </button>
          </div>
        </div>
      </div>
    </div>
    <!-- Invoice Modal -->
    <div v-if="showInvoiceModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
      <div class="bg-white rounded-lg shadow-xl w-1/3 max-h-[90vh] overflow-y-auto">
        <div class="p-6">
          <div class="flex justify-between items-center mb-4">
            <h2 class="text-xl font-bold text-gray-800">Invoice</h2>
            <button @click="showInvoiceModal = false" class="text-gray-500 hover:text-gray-700 cursor-pointer">
              <i class="fas fa-times"></i>
            </button>
          </div>
          <div class="border-t border-b border-gray-200 py-4 mb-4">
            <div class="text-center mb-4">
              <h1 class="text-2xl font-bold text-indigo-700">FoodiePoint Restaurant</h1>
              <p class="text-gray-600">123 Main Street, Cityville</p>
              <p class="text-gray-600">Tel: (123) 456-7890</p>
            </div>
            <div class="mb-4">
              <div class="flex justify-between mb-1">
                <span class="text-gray-600">Invoice #:</span>
                <span>INV-{{ invoiceNumber }}</span>
              </div>
              <div class="flex justify-between mb-1">
                <span class="text-gray-600">Date:</span>
                <span>{{ currentDate }}</span>
              </div>
              <div class="flex justify-between mb-1">
                <span class="text-gray-600">Table:</span>
                <span>{{ selectedTable?.number }}</span>
              </div>
              <div class="flex justify-between mb-1">
                <span class="text-gray-600">Customer:</span>
                <span>{{ selectedCustomer?.name || 'Walk-in Customer' }}</span>
              </div>
              <div class="flex justify-between mb-1">
                <span class="text-gray-600">Server:</span>
                <span>{{ selectedWaiter?.name || 'Not Assigned' }}</span>
              </div>
            </div>
            <div class="mb-4">
              <div class="flex font-medium text-gray-700 border-b pb-2 mb-2">
                <div class="w-1/2">Item</div>
                <div class="w-1/6 text-center">Qty</div>
                <div class="w-1/6 text-right">Price</div>
                <div class="w-1/6 text-right">Total</div>
              </div>
              <div v-for="(item, index) in currentOrder.items" :key="index" class="flex py-1">
                <div class="w-1/2">{{ item.name }}</div>
                <div class="w-1/6 text-center">{{ item.quantity }}</div>
                <div class="w-1/6 text-right">${{ item.price.toFixed(2) }}</div>
                <div class="w-1/6 text-right">${{ (item.price * item.quantity).toFixed(2) }}</div>
              </div>
            </div>
            <div class="border-t pt-2">
              <div class="flex justify-between mb-1">
                <span class="text-gray-600">Subtotal:</span>
                <span>${{ calculateSubtotal().toFixed(2) }}</span>
              </div>
              <div class="flex justify-between mb-1">
                <span class="text-gray-600">Tax (10%):</span>
                <span>${{ calculateTax().toFixed(2) }}</span>
              </div>
              <div class="flex justify-between mb-1">
                <span class="text-gray-600">Discount:</span>
                <span>${{ parseFloat(discountAmount || 0).toFixed(2) }}</span>
              </div>
              <div class="flex justify-between font-bold text-lg">
                <span>Total:</span>
                <span>${{ (calculateTotal() - parseFloat(discountAmount || 0)).toFixed(2) }}</span>
              </div>
            </div>
            <div class="mt-4 text-center text-gray-600">
              <p>Thank you for dining with us!</p>
              <p>Please come again.</p>
            </div>
          </div>
          <div class="flex space-x-3">
            <button @click="printInvoice"
              class="flex-1 bg-indigo-600 text-white py-3 rounded-md font-medium hover:bg-indigo-700 flex items-center justify-center cursor-pointer !rounded-button whitespace-nowrap">
              <i class="fas fa-print mr-2"></i>
              Print Invoice
            </button>
            <button @click="finishOrder"
              class="flex-1 bg-green-600 text-white py-3 rounded-md font-medium hover:bg-green-700 cursor-pointer !rounded-button whitespace-nowrap">
              Finish
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script lang="ts" setup>
import { ref, computed, onMounted } from 'vue';

// POS Profile Management
const showPosProfileModal = ref(true);
const showPosProfileDropdown = ref(false);
const selectedPosProfile = ref(null);
const posName = ref('');

const posProfiles = ref([
  { id: 1, name: 'Restaurant Standard', type: 'restaurant' },
  { id: 2, name: 'Quick Service', type: 'fast-food' },
  { id: 3, name: 'Bar & Grill', type: 'bar' },
  { id: 4, name: 'Cafe Profile', type: 'cafe' },
  { id: 5, name: 'Fine Dining', type: 'fine-dining' }
]);

const togglePosProfileDropdown = () => {
  showPosProfileDropdown.value = !showPosProfileDropdown.value;
};

const selectPosProfile = (profile) => {
  selectedPosProfile.value = profile;
  showPosProfileDropdown.value = false;
};

const canConfirmPosProfile = computed(() => {
  return selectedPosProfile.value && posName.value.trim().length > 0;
});

const confirmPosProfile = () => {
  if (canConfirmPosProfile.value) {
    showPosProfileModal.value = false;
    // Here you can add logic to save the POS profile settings
  }
};

// Mobile menu state
const showMobileMenu = ref(false);
const toggleMobileMenu = () => {
  showMobileMenu.value = !showMobileMenu.value;
};
// View mode (grid or list)
const viewMode = ref('grid');
// Close mobile menu on window resize
onMounted(() => {
  window.addEventListener('resize', () => {
    if (window.innerWidth >= 768) {
      showMobileMenu.value = false;
    }
  });
});
// Date and Time
const currentDate = ref('');
const currentTime = ref('');
// Update date and time
const updateDateTime = () => {
  const now = new Date();
  currentDate.value = now.toLocaleDateString('en-US', {
    weekday: 'long',
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  });
  currentTime.value = now.toLocaleTimeString('en-US', {
    hour: '2-digit',
    minute: '2-digit'
  });
};
onMounted(() => {
  updateDateTime();
  setInterval(updateDateTime, 60000); // Update every minute
});
// Rooms and Tables
const rooms = ref([
  {
    id: 1,
    name: 'Main Hall',
    tables: [
      { id: 1, number: 1, seats: 4, status: 'available' },
      { id: 2, number: 2, seats: 2, status: 'occupied' },
      { id: 3, number: 3, seats: 6, status: 'available' },
      { id: 4, number: 4, seats: 4, status: 'reserved' },
      { id: 5, number: 5, seats: 8, status: 'available' },
      { id: 6, number: 6, seats: 4, status: 'available' },
      { id: 7, number: 7, seats: 2, status: 'occupied' },
      { id: 8, number: 8, seats: 4, status: 'available' },
      { id: 9, number: 9, seats: 6, status: 'available' },
      { id: 10, number: 10, seats: 4, status: 'reserved' },
    ]
  },
  {
    id: 2,
    name: 'Private Rooms',
    tables: [
      { id: 11, number: 11, seats: 8, status: 'available' },
      { id: 12, number: 12, seats: 10, status: 'reserved' },
      { id: 13, number: 13, seats: 6, status: 'available' },
      { id: 14, number: 14, seats: 12, status: 'occupied' },
      { id: 15, number: 15, seats: 8, status: 'available' },
    ]
  },
  {
    id: 3,
    name: 'Outdoor',
    tables: [
      { id: 16, number: 16, seats: 4, status: 'available' },
      { id: 17, number: 17, seats: 2, status: 'available' },
      { id: 18, number: 18, seats: 6, status: 'occupied' },
      { id: 19, number: 19, seats: 4, status: 'available' },
      { id: 20, number: 20, seats: 8, status: 'reserved' },
    ]
  },
  {
    id: 4,
    name: 'Bar Area',
    tables: [
      { id: 21, number: 21, seats: 2, status: 'available' },
      { id: 22, number: 22, seats: 2, status: 'occupied' },
      { id: 23, number: 23, seats: 4, status: 'available' },
      { id: 24, number: 24, seats: 2, status: 'available' },
      { id: 25, number: 25, seats: 2, status: 'reserved' },
    ]
  },
  {
    id: 5,
    name: 'Lounge',
    tables: [
      { id: 26, number: 26, seats: 4, status: 'available' },
      { id: 27, number: 27, seats: 6, status: 'available' },
      { id: 28, number: 28, seats: 8, status: 'occupied' },
      { id: 29, number: 29, seats: 4, status: 'reserved' },
      { id: 30, number: 30, seats: 6, status: 'available' },
    ]
  }
]);
const selectedRoom = ref(rooms.value[0]);
const selectedTable = ref(null);
const selectRoom = (room) => {
  selectedRoom.value = room;
  selectedTable.value = null;
  // Update prices in current order if any items exist
  if (currentOrder.value.items.length > 0) {
    currentOrder.value.items = currentOrder.value.items.map(item => ({
      ...item,
      price: getAdjustedPrice(menuItems.value.find(mi => mi.id === item.id).price, room.name)
    }));
  }
};
const selectTable = (table) => {
  selectedTable.value = table;
  if (table.status === 'occupied') {
    // Load existing order for occupied table
    const existingOrder = getTableOrder(table.id);
    if (existingOrder) {
      currentOrder.value = JSON.parse(JSON.stringify(existingOrder)); // Deep copy
    } else {
      currentOrder.value = { items: [] };
    }
  } else {
    currentOrder.value = { items: [] };
  }
};
const getTableStatusClass = (table) => {
  switch (table.status) {
    case 'available':
      return 'border-green-500 bg-green-50 text-green-700';
    case 'occupied':
      return 'border-red-500 bg-red-50 text-red-700';
    case 'reserved':
      return 'border-yellow-500 bg-yellow-50 text-yellow-700';
    default:
      return 'border-gray-300';
  }
};
// Menu Categories and Items
const menuCategories = ref([
  { id: 0, name: 'All Items', icon: 'fas fa-th-list' },
  { id: 1, name: 'Starters', icon: 'fas fa-utensils' },
  { id: 2, name: 'Main Course', icon: 'fas fa-hamburger' },
  { id: 3, name: 'Desserts', icon: 'fas fa-ice-cream' },
  { id: 4, name: 'Beverages', icon: 'fas fa-glass-martini-alt' },
  { id: 5, name: 'Sides', icon: 'fas fa-drumstick-bite' },
  { id: 6, name: 'Specials', icon: 'fas fa-star' }
]);
const selectedCategory = ref(menuCategories.value[0]);
const searchQuery = ref('');
const menuItems = ref([
  {
    id: 1,
    categoryId: 1,
    name: 'Garlic Bread',
    price: 5.99,
    description: 'Toasted bread with garlic butter',
    image: 'https://readdy.ai/api/search-image?query=Freshly%20baked%20garlic%20bread%20with%20melted%20butter%20and%20herbs%20on%20a%20wooden%20serving%20board%2C%20with%20a%20clean%20white%20background%2C%20professional%20food%20photography%2C%20high%20resolution%2C%20appetizing%20starter%20dish&width=300&height=200&seq=1&orientation=landscape'
  },
  {
    id: 2,
    categoryId: 1,
    name: 'Mozzarella Sticks',
    price: 7.99,
    description: 'Fried cheese sticks with marinara',
    image: 'https://readdy.ai/api/search-image?query=Golden%20fried%20mozzarella%20sticks%20with%20crispy%20exterior%20and%20melted%20cheese%20inside%2C%20served%20with%20marinara%20dipping%20sauce%2C%20on%20white%20plate%2C%20clean%20background%2C%20professional%20food%20photography&width=300&height=200&seq=2&orientation=landscape'
  },
  {
    id: 3,
    categoryId: 1,
    name: 'Bruschetta',
    price: 6.99,
    description: 'Tomato and basil on toasted bread',
    image: 'https://readdy.ai/api/search-image?query=Italian%20bruschetta%20with%20diced%20tomatoes%2C%20fresh%20basil%2C%20and%20balsamic%20glaze%20on%20toasted%20bread%2C%20arranged%20on%20white%20serving%20plate%2C%20clean%20background%2C%20professional%20food%20photography%2C%20appetizer&width=300&height=200&seq=3&orientation=landscape'
  },
  {
    id: 4,
    categoryId: 2,
    name: 'Grilled Salmon',
    price: 18.99,
    description: 'With lemon butter sauce',
    image: 'https://readdy.ai/api/search-image?query=Perfectly%20grilled%20salmon%20fillet%20with%20lemon%20butter%20sauce%2C%20garnished%20with%20herbs%2C%20served%20on%20white%20plate%2C%20clean%20background%2C%20professional%20food%20photography%2C%20main%20course%20dish%2C%20high%20resolution&width=300&height=200&seq=4&orientation=landscape'
  },
  {
    id: 5,
    categoryId: 2,
    name: 'Beef Steak',
    price: 24.99,
    description: 'Grilled to perfection',
    image: 'https://readdy.ai/api/search-image?query=Juicy%20medium-rare%20beef%20steak%20with%20grill%20marks%2C%20seasoned%20with%20herbs%20and%20spices%2C%20on%20white%20plate%2C%20clean%20background%2C%20professional%20food%20photography%2C%20main%20course%2C%20high%20resolution&width=300&height=200&seq=5&orientation=landscape'
  },
  {
    id: 6,
    categoryId: 2,
    name: 'Chicken Alfredo',
    price: 16.99,
    description: 'Creamy pasta with grilled chicken',
    image: 'https://readdy.ai/api/search-image?query=Creamy%20fettuccine%20alfredo%20pasta%20with%20grilled%20chicken%20strips%2C%20garnished%20with%20parsley%2C%20served%20in%20white%20bowl%2C%20clean%20background%2C%20professional%20food%20photography%2C%20main%20course%2C%20high%20resolution&width=300&height=200&seq=6&orientation=landscape'
  },
  {
    id: 7,
    categoryId: 3,
    name: 'Chocolate Cake',
    price: 8.99,
    description: 'Rich and decadent',
    image: 'https://readdy.ai/api/search-image?query=Slice%20of%20rich%20chocolate%20cake%20with%20layers%20of%20chocolate%20frosting%2C%20served%20on%20white%20plate%2C%20clean%20background%2C%20professional%20food%20photography%2C%20dessert%2C%20high%20resolution&width=300&height=200&seq=7&orientation=landscape'
  },
  {
    id: 8,
    categoryId: 3,
    name: 'Cheesecake',
    price: 7.99,
    description: 'New York style',
    image: 'https://readdy.ai/api/search-image?query=Slice%20of%20creamy%20New%20York%20style%20cheesecake%20with%20graham%20cracker%20crust%2C%20served%20on%20white%20plate%2C%20clean%20background%2C%20professional%20food%20photography%2C%20dessert%2C%20high%20resolution&width=300&height=200&seq=8&orientation=landscape'
  },
  {
    id: 9,
    categoryId: 3,
    name: 'Ice Cream',
    price: 5.99,
    description: 'Vanilla, chocolate, or strawberry',
    image: 'https://readdy.ai/api/search-image?query=Scoops%20of%20vanilla%2C%20chocolate%2C%20and%20strawberry%20ice%20cream%20in%20glass%20bowl%20with%20wafer%2C%20clean%20background%2C%20professional%20food%20photography%2C%20dessert%2C%20high%20resolution&width=300&height=200&seq=9&orientation=landscape'
  },
  {
    id: 10,
    categoryId: 4,
    name: 'Soda',
    price: 2.99,
    description: 'Various flavors available',
    image: 'https://readdy.ai/api/search-image?query=Glass%20of%20cola%20soda%20with%20ice%20cubes%20and%20bubbles%2C%20condensation%20on%20glass%2C%20clean%20background%2C%20professional%20beverage%20photography%2C%20refreshing%20drink%2C%20high%20resolution&width=300&height=200&seq=10&orientation=landscape'
  },
  {
    id: 11,
    categoryId: 4,
    name: 'Iced Tea',
    price: 3.49,
    description: 'Sweet or unsweetened',
    image: 'https://readdy.ai/api/search-image?query=Glass%20of%20iced%20tea%20with%20lemon%20slice%20and%20mint%20garnish%2C%20condensation%20on%20glass%2C%20clean%20background%2C%20professional%20beverage%20photography%2C%20refreshing%20drink%2C%20high%20resolution&width=300&height=200&seq=11&orientation=landscape'
  },
  {
    id: 12,
    categoryId: 4,
    name: 'Coffee',
    price: 3.99,
    description: 'Freshly brewed',
    image: 'https://readdy.ai/api/search-image?query=Cup%20of%20freshly%20brewed%20coffee%20with%20steam%20rising%2C%20in%20white%20ceramic%20cup%20on%20saucer%2C%20clean%20background%2C%20professional%20beverage%20photography%2C%20morning%20drink%2C%20high%20resolution&width=300&height=200&seq=12&orientation=landscape'
  },
  {
    id: 13,
    categoryId: 5,
    name: 'French Fries',
    price: 4.99,
    description: 'Crispy and golden',
    image: 'https://readdy.ai/api/search-image?query=Basket%20of%20crispy%20golden%20french%20fries%20with%20ketchup%20dipping%20sauce%2C%20clean%20background%2C%20professional%20food%20photography%2C%20side%20dish%2C%20high%20resolution&width=300&height=200&seq=13&orientation=landscape'
  },
  {
    id: 14,
    categoryId: 5,
    name: 'Onion Rings',
    price: 5.49,
    description: 'Beer battered',
    image: 'https://readdy.ai/api/search-image?query=Stack%20of%20beer%20battered%20onion%20rings%2C%20golden%20and%20crispy%2C%20served%20with%20dipping%20sauce%2C%20clean%20background%2C%20professional%20food%20photography%2C%20side%20dish%2C%20high%20resolution&width=300&height=200&seq=14&orientation=landscape'
  },
  {
    id: 15,
    categoryId: 5,
    name: 'Side Salad',
    price: 4.49,
    description: 'Fresh greens and vegetables',
    image: 'https://readdy.ai/api/search-image?query=Small%20bowl%20of%20fresh%20green%20salad%20with%20mixed%20vegetables%20and%20vinaigrette%20dressing%2C%20clean%20background%2C%20professional%20food%20photography%2C%20side%20dish%2C%20healthy%20option%2C%20high%20resolution&width=300&height=200&seq=15&orientation=landscape'
  },
  {
    id: 16,
    categoryId: 6,
    name: 'Chef Special Pasta',
    price: 19.99,
    description: 'Secret recipe',
    image: 'https://readdy.ai/api/search-image?query=Gourmet%20pasta%20dish%20with%20special%20sauce%2C%20garnished%20with%20herbs%20and%20parmesan%2C%20served%20in%20elegant%20white%20bowl%2C%20clean%20background%2C%20professional%20food%20photography%2C%20special%20dish%2C%20high%20resolution&width=300&height=200&seq=16&orientation=landscape'
  },
  {
    id: 17,
    categoryId: 6,
    name: 'Seafood Platter',
    price: 29.99,
    description: 'Assortment of fresh seafood',
    image: 'https://readdy.ai/api/search-image?query=Elegant%20platter%20of%20assorted%20seafood%20including%20shrimp%2C%20mussels%2C%20and%20fish%2C%20garnished%20with%20lemon%20and%20herbs%2C%20clean%20background%2C%20professional%20food%20photography%2C%20special%20dish%2C%20high%20resolution&width=300&height=200&seq=17&orientation=landscape'
  },
  {
    id: 18,
    categoryId: 6,
    name: 'Truffle Risotto',
    price: 22.99,
    description: 'Creamy rice with truffle',
    image: 'https://readdy.ai/api/search-image?query=Creamy%20truffle%20risotto%20with%20parmesan%20shavings%20and%20herbs%2C%20served%20in%20elegant%20white%20bowl%2C%20clean%20background%2C%20professional%20food%20photography%2C%20gourmet%20special%20dish%2C%20high%20resolution&width=300&height=200&seq=18&orientation=landscape'
  }
]);
const selectCategory = (category) => {
  selectedCategory.value = category;
};
// Price multipliers for each room
const roomPriceMultipliers = {
  'Main Hall': 1,
  'Private Rooms': 1.3,
  'Outdoor': 1.1,
  'Bar Area': 1.2,
  'Lounge': 1.15
};
// Function to get adjusted price based on room
const getAdjustedPrice = (basePrice: number, roomName: string) => {
  const multiplier = roomPriceMultipliers[roomName] || 1;
  return +(basePrice * multiplier).toFixed(2);
};
const filteredMenuItems = computed(() => {
  let items = menuItems.value;
  // Filter by category if not "All Items"
  if (selectedCategory.value.id !== 0) {
    items = items.filter(item => item.categoryId === selectedCategory.value.id);
  }
  // Apply search filter if query exists
  if (searchQuery.value) {
    const query = searchQuery.value.toLowerCase();
    items = items.filter(item =>
      item.name.toLowerCase().includes(query) ||
      item.description.toLowerCase().includes(query)
    );
  }
  // Adjust prices based on selected room
  return items.map(item => ({
    ...item,
    price: getAdjustedPrice(item.price, selectedRoom.value.name)
  }));
});
// Order Management
const currentOrder = ref({
  items: []
});
// Store orders for occupied tables
const tableOrders = ref({
  2: {
    items: [
      { id: 1, name: 'Garlic Bread', price: 5.99, quantity: 2 },
      { id: 4, name: 'Grilled Salmon', price: 18.99, quantity: 1 }
    ]
  },
  7: {
    items: [
      { id: 5, name: 'Beef Steak', price: 24.99, quantity: 1 },
      { id: 13, name: 'French Fries', price: 4.99, quantity: 2 }
    ]
  },
  14: {
    items: [
      { id: 6, name: 'Chicken Alfredo', price: 16.99, quantity: 2 },
      { id: 10, name: 'Soda', price: 2.99, quantity: 2 }
    ]
  },
  18: {
    items: [
      { id: 16, name: 'Chef Special Pasta', price: 19.99, quantity: 1 },
      { id: 11, name: 'Iced Tea', price: 3.49, quantity: 2 }
    ]
  },
  22: {
    items: [
      { id: 2, name: 'Mozzarella Sticks', price: 7.99, quantity: 1 },
      { id: 12, name: 'Coffee', price: 3.99, quantity: 2 }
    ]
  },
  28: {
    items: [
      { id: 17, name: 'Seafood Platter', price: 29.99, quantity: 1 },
      { id: 15, name: 'Side Salad', price: 4.49, quantity: 1 }
    ]
  }
});
const getTableOrder = (tableId) => {
  return tableOrders.value[tableId] || null;
};
const updateTableOrder = () => {
  if (selectedTable.value && selectedTable.value.status === 'occupied') {
    tableOrders.value[selectedTable.value.id] = JSON.parse(JSON.stringify(currentOrder.value));
  }
};
const addItemToOrder = (item) => {
  const existingItem = currentOrder.value.items.find(i => i.id === item.id);
  if (existingItem) {
    existingItem.quantity += 1;
  } else {
    currentOrder.value.items.push({
      id: item.id,
      name: item.name,
      price: item.price,
      quantity: 1
    });
  }
  updateTableOrder();
};
const increaseItemQuantity = (index) => {
  currentOrder.value.items[index].quantity += 1;
  updateTableOrder();
};
const decreaseItemQuantity = (index) => {
  if (currentOrder.value.items[index].quantity > 1) {
    currentOrder.value.items[index].quantity -= 1;
  } else {
    removeItemFromOrder(index);
  }
  updateTableOrder();
};
const removeItemFromOrder = (index) => {
  currentOrder.value.items.splice(index, 1);
  updateTableOrder();
};
const clearOrder = () => {
  currentOrder.value.items = [];
  selectedTable.value = null;
};
const calculateSubtotal = () => {
  return currentOrder.value.items.reduce((total, item) => {
    return total + (item.price * item.quantity);
  }, 0);
};
const calculateTax = () => {
  return calculateSubtotal() * 0.1; // 10% tax
};
const calculateTotal = () => {
  return calculateSubtotal() + calculateTax();
};
const canPlaceOrder = computed(() => {
  return selectedTable.value && currentOrder.value.items.length > 0;
});
// Payment Processing
const showPaymentModal = ref(false);
const showInvoiceModal = ref(false);
const selectedPaymentMethod = ref('cash');
const discountAmount = ref('');
// Customer and Waiter Management
const customers = ref([
  { id: 1, name: 'John Doe', phone: '123-456-7890' },
  { id: 2, name: 'Jane Smith', phone: '234-567-8901' },
  { id: 3, name: 'Mike Johnson', phone: '345-678-9012' },
  { id: 4, name: 'Sarah Williams', phone: '456-789-0123' },
  { id: 5, name: 'David Brown', phone: '567-890-1234' }
]);
const waiters = ref([
  { id: 1, name: 'Alex Thompson', status: 'active' },
  { id: 2, name: 'Maria Garcia', status: 'active' },
  { id: 3, name: 'James Wilson', status: 'active' },
  { id: 4, name: 'Emily Davis', status: 'break' },
  { id: 5, name: 'Robert Taylor', status: 'active' }
]);
const selectedCustomer = ref(null);
const selectedWaiter = ref(null);
const showCustomerDropdown = ref(false);
const showWaiterDropdown = ref(false);
const showAddCustomerModal = ref(false);
const selectCustomer = (customer) => {
  selectedCustomer.value = customer;
  showCustomerDropdown.value = false;
};
const selectWaiter = (waiter) => {
  selectedWaiter.value = waiter;
  showWaiterDropdown.value = false;
};
const invoiceNumber = ref(Math.floor(10000 + Math.random() * 90000));
const paymentMethods = ref([
  { id: 'cash', name: 'Cash', icon: 'fas fa-money-bill-wave' },
  { id: 'card', name: 'Card', icon: 'fas fa-credit-card' },
  { id: 'digital', name: 'Digital', icon: 'fas fa-mobile-alt' }
]);
const printKOT = () => {
  // In a real application, this would send the order to the kitchen printer
  alert('Kitchen Order Ticket sent to printer!');
};
const proceedToPayment = () => {
  showPaymentModal.value = true;
};
const processPayment = () => {
  showPaymentModal.value = false;
  showInvoiceModal.value = true;
};
const printInvoice = () => {
  // In a real application, this would print the invoice
  alert('Invoice sent to printer!');
};
const finishOrder = () => {
  // Update table status
  if (selectedTable.value) {
    const table = selectedRoom.value.tables.find(t => t.id === selectedTable.value.id);
    if (table) {
      table.status = 'available';
      // Remove the order from tableOrders when finished
      if (tableOrders.value[table.id]) {
        delete tableOrders.value[table.id];
      }
    }
  }
  // Clear the current order
  clearOrder();
  // Close the invoice modal
  showInvoiceModal.value = false;
};
</script>
<style scoped>
/* Responsive adjustments */
@media (max-width: 640px) {
  .container {
    padding-left: 1rem;
    padding-right: 1rem;
  }
}

/* Improve tap targets on mobile */
@media (max-width: 768px) {

  button,
  [role="button"],
  .cursor-pointer {
    min-height: 44px;
    min-width: 44px;
  }

  input,
  select {
    min-height: 44px;
  }
}

/* Custom scrollbar */
::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

::-webkit-scrollbar-track {
  background: #f1f1f1;
  border-radius: 4px;
}

::-webkit-scrollbar-thumb {
  background: #c1c1c1;
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: #a1a1a1;
}

/* Horizontal scrollbar for tables */
.overflow-x-auto {
  scrollbar-width: thin;
  scrollbar-color: #c1c1c1 #f1f1f1;
}

.overflow-x-auto::-webkit-scrollbar {
  height: 6px;
}

.overflow-x-auto::-webkit-scrollbar-track {
  background: #f1f1f1;
  border-radius: 3px;
}

.overflow-x-auto::-webkit-scrollbar-thumb {
  background: #c1c1c1;
  border-radius: 3px;
}

.overflow-x-auto::-webkit-scrollbar-thumb:hover {
  background: #a1a1a1;
}

/* Remove number input arrows */
input[type="number"]::-webkit-inner-spin-button,
input[type="number"]::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input[type="number"] {
  -moz-appearance: textfield;
}

/* Smooth scrolling */
.overflow-y-auto {
  scroll-behavior: smooth;
}

/* POS Profile Modal Styles */
.pos-profile-modal-enter-active,
.pos-profile-modal-leave-active {
  transition: opacity 0.3s ease;
}

.pos-profile-modal-enter-from,
.pos-profile-modal-leave-to {
  opacity: 0;
}

/* Hide scrollbar for Chrome, Safari and Opera */
.overflow-y-auto::-webkit-scrollbar {
  display: none;
}

/* Hide scrollbar for IE, Edge and Firefox */
.overflow-y-auto {
  -ms-overflow-style: none;
  /* IE and Edge */
  scrollbar-width: none;
  /* Firefox */
}
</style>