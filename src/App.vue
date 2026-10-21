<script setup>
import { computed, onMounted, reactive, ref } from 'vue'
import confetti from 'canvas-confetti'
import {
  User,
  Phone,
  Check,
  CheckCircle2,
  Calendar,
  Copy,
  Plus,
  Minus,
  CreditCard,
  Shirt,
  Building2,
  Briefcase,
  IdCard,
  MapPin,
  PartyPopper,
  Users,
  Heart,
  Baby,
  Bus,
  Car,
  Info,
  ShieldCheck,
  Ticket,
  Share2,
  ArrowRight,
  HelpCircle,
  GraduationCap,
  ChevronDown,
  UserPlus
} from '@lucide/vue'
import quantumLogo from './quantum-logo.png'

const registrationFee = 100
const isComplete = ref(false)
const isSubmitting = ref(false)
const copied = ref(false)
const registrationRefNumber = ref('')

const tShirtSizes = [
  { code: 'M', label: 'M (27")', length: '27"' },
  { code: 'L', label: 'L (28")', length: '28"' },
  { code: 'XL', label: 'XL (29")', length: '29"' },
  { code: 'XXL', label: 'XXL (30")', length: '30"' },
  { code: '3XL', label: '3XL (31")', length: '31"' },
]

const paymentMethods = [
  {
    id: 'bkash',
    name: 'bKash',
    bnName: 'বিকাশ',
    number: '01712-345678',
    type: 'send money',
    brandColor: '#E2136E',
    activeBg: 'bg-pink-50 border-pink-500',
    instructions: 'বিকাশ অ্যাপ বা *247# ডায়াল করে send money করুন।'
  },
  {
    id: 'nagad',
    name: 'nagad',
    bnName: 'নগদ',
    number: '01812-345678',
    type: 'send money',
    brandColor: '#F7941D',
    activeBg: 'bg-orange-50 border-orange-500',
    instructions: 'নগদ অ্যাপ বা *167# ডায়াল করে send money করুন।'
  }
]

const transportOptions = [
  {
    id: 'self',
    title: 'নিজ দ্বায়িত্বে',
    englishTitle: 'Self Arrangement',
    fee: 0,
    feeText: '০ টাকা',
    badge: '০ টাকা',
    description: 'নিজস্ব ব্যবস্থাপনায় কোয়ান্টামমে যাওয়া-আসা করবেন'
  },
  {
    id: 'dhaka',
    title: 'ঢাকা টিমের সাথে যাওয়া আসা (৫০০টাকা)',
    englishTitle: 'Round trip with Dhaka Team (500 TK)',
    fee: 500,
    feeText: '৫০০ টাকা',
    badge: '৳৫০০',
    description: 'ঢাকা থেকে কোয়ান্টামমে যাওয়া-আসা ও দলগত সমন্বয়'
  },
  {
    id: 'chattogram',
    title: 'চট্টগ্রাম টিমের সাথে যাওয়া আসা (২৫০টাকা)',
    englishTitle: 'Round trip with Chattogram Team (250 TK)',
    fee: 250,
    feeText: '২৫০ টাকা',
    badge: '৳২৫০',
    description: 'চট্টগ্রাম থেকে কোয়ান্টামমে যাওয়া-আসা ও দলগত সমন্বয়'
  }
]

const form = reactive({
  fullName: '',
  quantaId: '',
  gender: 'male',
  mobileNumber: '',
  occupation: '',
  institution: '',
  batch: '',
  tShirtSize: 'L',
  hasSpouse: false,
  childrenCount: 0,
  transportOption: 'self',
  paymentMethod: 'bkash',
  transactionId: '',
})

const selectedTransport = computed(() => {
  return transportOptions.find((t) => t.id === form.transportOption) || transportOptions[0]
})

const attendeeCount = computed(() => {
  let count = 1
  if (form.hasSpouse) count += 1
  const children = parseInt(form.childrenCount, 10)
  if (!isNaN(children) && children > 0) {
    count += children
  }
  return count
})

const totalFee = computed(() => {
  return registrationFee + selectedTransport.value.fee
})

const selectedPaymentConfig = computed(() => {
  return paymentMethods.find((p) => p.id === form.paymentMethod) || paymentMethods[0]
})

const isMobileValid = computed(() => {
  return /^01[3-9]\d{8}$/.test(form.mobileNumber.replace(/\D/g, ''))
})

function incrementChildren() {
  const current = parseInt(form.childrenCount, 10) || 0
  form.childrenCount = current + 1
}

function decrementChildren() {
  const current = parseInt(form.childrenCount, 10) || 0
  if (current > 0) {
    form.childrenCount = current - 1
  }
}

function copyPaymentNumber(number) {
  const cleanNumber = number.replace(/[^0-9]/g, '')
  navigator.clipboard.writeText(cleanNumber || number).then(() => {
    copied.value = true
    setTimeout(() => {
      copied.value = false
    }, 2500)
  })
}

function triggerFestiveConfetti() {
  const count = 180
  const defaults = { origin: { y: 0.65 } }

  function fire(particleRatio, opts) {
    confetti({
      ...defaults,
      ...opts,
      particleCount: Math.floor(count * particleRatio)
    })
  }

  fire(0.25, { spread: 26, startVelocity: 50, colors: ['#2563eb', '#38bdf8', '#0284c7', '#60a5fa'] })
  fire(0.2, { spread: 60, colors: ['#1d4ed8', '#93c5fd', '#3b82f6'] })
  fire(0.35, { spread: 100, decay: 0.91, scalar: 0.8, colors: ['#2563eb', '#06b6d4', '#10b981'] })
  fire(0.1, { spread: 120, startVelocity: 25, decay: 0.92, scalar: 1.2 })
  fire(0.1, { spread: 120, startVelocity: 45 })
}

function completeRegistration() {
  if (isSubmitting.value || isComplete.value) return
  isSubmitting.value = true

  const randomSuffix = Math.floor(10000 + Math.random() * 90000)
  registrationRefNumber.value = `qcsc-2027-${randomSuffix}`

  try {
    localStorage.setItem('reunion2027_registered', JSON.stringify({
      ref: registrationRefNumber.value,
      form: { ...form },
      submittedAt: new Date().toISOString()
    }))
  } catch (e) {
    // ignore
  }

  isComplete.value = true
  isSubmitting.value = false
  window.scrollTo({ top: 0, behavior: 'smooth' })
  setTimeout(() => {
    triggerFestiveConfetti()
  }, 200)
}

onMounted(() => {
  try {
    const saved = localStorage.getItem('reunion2027_registered')
    if (saved) {
      const data = JSON.parse(saved)
      if (data && data.ref) {
        registrationRefNumber.value = data.ref
        if (data.form) {
          Object.assign(form, data.form)
        }
        isComplete.value = true
      }
    }
  } catch (e) {
    // ignore
  }
})

function shareViaWhatsApp() {
  const text = `আমি ঘরে ফেরা আনন্দ উৎসব ২০২৭ (কোয়ান্টা রিইউনিয়ন, কোয়ান্টাম কসমো স্কুল ও কলেজ)-এ নিবন্ধন করেছি। আপনিও যোগ দিন: ${window.location.href}`
  window.open(`https://api.whatsapp.com/send?text=${encodeURIComponent(text)}`, '_blank')
}

function addToGoogleCalendar() {
  const title = encodeURIComponent('ঘরে ফেরা আনন্দ উৎসব ২০২৭ - কোয়ান্টা রিইউনিয়ন')
  const details = encodeURIComponent('কোয়ান্টাম কসমো স্কুল ও কলেজ কোয়ান্টা রিইউনিয়ন প্রোগ্রাম। স্মৃতিচারণ, মিলনমেলা ও সাংস্কৃতিক আয়োজন।')
  const location = encodeURIComponent('কোয়ান্টামম, লামা, বান্দরবান (Quantumom, Lama, Bandarban)')
  const dates = '20261216T030000Z/20261217T150000Z'
  const url = `https://calendar.google.com/calendar/render?action=TEMPLATE&text=${title}&details=${details}&location=${location}&dates=${dates}`
  window.open(url, '_blank')
}

function startNewRegistration() {
  form.fullName = ''
  form.quantaId = ''
  form.gender = 'male'
  form.mobileNumber = ''
  form.occupation = ''
  form.institution = ''
  form.batch = ''
  form.tShirtSize = 'L'
  form.hasSpouse = false
  form.childrenCount = 0
  form.transportOption = 'self'
  form.paymentMethod = 'bkash'
  form.transactionId = ''

  registrationRefNumber.value = ''
  isComplete.value = false

  try {
    localStorage.removeItem('reunion2027_registered')
  } catch (e) {
    // ignore
  }

  window.scrollTo({ top: 0, behavior: 'smooth' })
}
</script>

<template>
  <div class="min-h-screen bg-[#f1f6fd] text-[#0f2747] selection:bg-blue-600 selection:text-white pb-24 lg:pb-0">

    <div class="mx-auto max-w-7xl px-3 sm:px-6 sm:py-8 lg:px-8 py-5">
      
      <!-- SUCCESS SCREEN: REGISTRATION SUBMISSION CONFIRMATION -->
      <section v-if="isComplete" class="mx-auto max-w-2xl py-4 sm:py-8">
        <div class="rounded-2xl sm:rounded-3xl bg-white p-6 sm:p-9 border border-slate-200 text-center">
          <!-- Logo & Success Badge -->
          <div class="flex items-center justify-center gap-3 mb-4">
            <div class="flex size-14 sm:size-16 shrink-0 items-center justify-center rounded-full bg-white p-1.5 border border-slate-200 overflow-hidden">
              <img :src="quantumLogo" alt="Quantum Cosmo School & College Logo" class="size-full rounded-full object-contain" />
            </div>
          </div>

          <span class="inline-block rounded-full bg-emerald-50 px-3.5 py-1 text-xs sm:text-sm font-semibold text-emerald-700 border border-emerald-200">
            নিবন্ধন সফলভাবে জমা হয়েছে • registration submitted
          </span>

          <h1 class="mt-3 text-2xl font-bold text-[#0c234a] sm:text-3xl lg:text-4xl">
            আপনার নিবন্ধন সফলভাবে জমা হয়েছে
          </h1>
          <p class="mt-1 text-xs sm:text-sm font-medium text-slate-500">
            your registration has been successfully submitted
          </p>

          <!-- Core Call Notification Notice -->
          <div class="mt-6 rounded-2xl bg-blue-50/80 p-4 sm:p-5 text-left border border-blue-200">
            <div class="flex items-start gap-3.5 sm:gap-4">
              <div class="flex size-11 shrink-0 items-center justify-center rounded-xl bg-blue-700 text-white">
                <Phone class="size-5.5" />
              </div>
              <div class="min-w-0">
                <h3 class="text-base sm:text-lg font-bold text-blue-950">
                  পেমেন্ট প্রাপ্তি নিশ্চিতে আমাদের টিম আপনাকে কল করবে
                </h3>
                <p class="mt-1 text-sm sm:text-base font-normal text-blue-900 leading-relaxed">
                  আমাদের প্রতিনিধি অতি শীঘ্রই আপনার প্রদত্ত নম্বরে (<strong class="font-bold text-blue-950">{{ form.mobileNumber }}</strong>) কল করে আপনার পেমেন্ট প্রাপ্তি নিশ্চিত করবেন।
                </p>
                <p class="mt-2 text-xs sm:text-sm text-blue-700 font-medium">
                  our team will inform you by call to confirm your payment has been received as soon as possible.
                </p>
              </div>
            </div>
          </div>

          <!-- Submission Details Summary -->
          <div class="mt-6 rounded-2xl bg-slate-50 p-4 sm:p-5 text-left border border-slate-200">
            <div class="flex items-center justify-between pb-3 border-b border-slate-200">
              <span class="text-xs sm:text-sm font-semibold text-slate-600">রেজিস্ট্রেশন রেফারেন্স নম্বর (ref no.)</span>
              <span class="font-mono text-sm sm:text-base font-bold text-blue-900">{{ registrationRefNumber }}</span>
            </div>

            <div class="mt-3 grid gap-2.5 text-xs sm:text-sm">
              <div class="flex items-center justify-between">
                <span class="text-slate-600 font-medium">অংশগ্রহণকারীর নাম:</span>
                <span class="font-bold text-slate-900">{{ form.fullName }}</span>
              </div>
              <div class="flex items-center justify-between">
                <span class="text-slate-600 font-medium">কোয়ান্টা আইডি:</span>
                <span class="font-mono font-semibold text-blue-800">{{ form.quantaId }}</span>
              </div>
              <div class="flex items-center justify-between">
                <span class="text-slate-600 font-medium">মোবাইল নম্বর:</span>
                <span class="font-mono text-slate-800">{{ form.mobileNumber }}</span>
              </div>
              <div class="flex items-center justify-between">
                <span class="text-slate-600 font-medium">টি-শার্ট সাইজ ও জেন্ডার:</span>
                <span class="font-semibold text-slate-800">{{ form.tShirtSize }} • {{ form.gender }}</span>
              </div>
              <div class="flex items-center justify-between">
                <span class="text-slate-600 font-medium">Spouse (স্বামী/স্ত্রী):</span>
                <span class="font-semibold" :class="form.hasSpouse ? 'text-emerald-700 font-bold' : 'text-slate-600'">
                  {{ form.hasSpouse ? 'হ্যাঁ (সাথে আসবেন)' : 'না' }}
                </span>
              </div>
              <div v-if="form.childrenCount > 0" class="flex items-center justify-between">
                <span class="text-slate-600 font-medium">সন্তান সংখ্যা:</span>
                <span class="font-semibold text-slate-800">{{ form.childrenCount }} জন</span>
              </div>
              <div class="flex items-center justify-between">
                <span class="text-slate-600 font-medium">যাতায়াত ব্যবস্থা:</span>
                <span class="font-semibold text-slate-800">{{ selectedTransport.title }}</span>
              </div>
              <div class="flex items-center justify-between">
                <span class="text-slate-600 font-medium">মোট অংশগ্রহণকারী:</span>
                <span class="font-semibold text-slate-800">{{ attendeeCount }} জন</span>
              </div>
              <div class="flex items-center justify-between border-t border-slate-200 pt-2.5">
                <span class="text-slate-600 font-medium">পেমেন্ট মাধ্যম:</span>
                <span class="font-semibold text-slate-800">{{ form.paymentMethod }}</span>
              </div>
              <div v-if="form.transactionId" class="flex items-center justify-between">
                <span class="text-slate-600 font-medium">ট্রানজেকশন আইডি:</span>
                <span class="font-mono font-semibold text-blue-900">{{ form.transactionId }}</span>
              </div>
              <div class="flex items-center justify-between border-t border-slate-200 pt-2.5">
                <span class="font-bold text-slate-800">সর্বমোট প্রদেয় ফি:</span>
                <span class="text-base font-extrabold text-blue-900">৳{{ totalFee }}</span>
              </div>
            </div>
          </div>

          <!-- Action Buttons -->
          <div class="mt-6 flex flex-col sm:flex-row items-center justify-center gap-3">
            <button
              type="button"
              class="flex w-full sm:w-auto items-center justify-center gap-2 rounded-xl bg-blue-700 px-5 py-3 text-sm font-semibold text-white transition hover:bg-blue-800 min-h-[46px]"
              @click="addToGoogleCalendar"
            >
              <Calendar class="size-4.5 text-blue-200" />
              <span>ক্যালেন্ডারে যোগ করুন</span>
            </button>

            <button
              type="button"
              class="flex w-full sm:w-auto items-center justify-center gap-2 rounded-xl bg-emerald-50 px-5 py-3 text-sm font-semibold text-emerald-800 transition hover:bg-emerald-100 border border-emerald-200 min-h-[46px]"
              @click="shareViaWhatsApp"
            >
              <Share2 class="size-4.5" />
              <span>হোয়াটসঅ্যাপে শেয়ার করুন</span>
            </button>
          </div>

          <!-- New Registration Button -->
          <div class="mt-4 pt-4 border-t border-slate-200/80">
            <button
              type="button"
              class="flex w-full items-center justify-center gap-2.5 rounded-xl bg-blue-50/80 hover:bg-blue-100/90 text-blue-900 px-5 py-3.5 text-sm sm:text-base font-bold border-2 border-blue-600 transition hover:border-blue-700 active:scale-[0.99] min-h-[50px] shadow-xs"
              @click="startNewRegistration"
            >
              <UserPlus class="size-5 text-blue-700" />
              <span>নতুন রেজিস্ট্রেশন ফরম (New Registration Form)</span>
            </button>
            <p class="mt-2 text-xs text-slate-500 text-center">
              আরেকজন সদস্য বা সহপাঠীর নিবন্ধনের জন্য উপরের বাটনে চাপ দিন
            </p>
          </div>

          <!-- Submission Confirmation Note -->
          <div class="mt-4 rounded-xl bg-slate-50 p-4 border border-slate-200 text-xs sm:text-sm text-slate-600">
            <div class="flex items-center justify-center gap-1.5 font-bold text-slate-800">
              <ShieldCheck class="size-4.5 text-emerald-600" />
              <span>নিবন্ধন চূড়ান্তভাবে গৃহীত হয়েছে</span>
            </div>
            <p class="mt-1 text-slate-500">
              আপনার দেওয়া তথ্য সফলভাবে সংরক্ষিত হয়েছে। কোনো তথ্যের পরিবর্তন বা সহায়তার জন্য আমাদের হেল্পলাইনে যোগাযোগ করতে পারেন।
            </p>
          </div>
        </div>
      </section>

      <!-- REGISTRATION FORM SECTION -->
      <template v-else>
        <!-- Deep Premium Blue Hero Section -->
        <section class="relative overflow-hidden mb-5 sm:mb-6 rounded-xl bg-gradient-to-br from-[#020b18] via-[#061836] to-[#030e22] p-4 sm:p-8 lg:p-9 text-white border border-blue-500/25">
          <!-- Ambient lighting accents -->
          <div class="pointer-events-none absolute -top-24 -right-24 size-80 sm:size-96 rounded-full bg-blue-500/15 blur-3xl"></div>
          <div class="pointer-events-none absolute -bottom-24 -left-24 size-80 sm:size-96 rounded-full bg-indigo-500/10 blur-3xl"></div>

          <div class="relative grid gap-5 sm:gap-6 lg:grid-cols-[1fr_auto] lg:items-center">
            <div class="max-w-3xl">
              <!-- Official Branding & Registration Form Header Badge (Side-by-side on mobile & desktop) -->
              <div class="flex items-center gap-3 sm:gap-4 mb-3 sm:mb-4">
                <div class="flex size-14 sm:size-20 shrink-0 items-center justify-center rounded-full bg-white p-1 sm:p-2 border border-white/30 overflow-hidden">
                  <img :src="quantumLogo" alt="Quantum Cosmo School & College Logo" class="size-full rounded-full object-contain" />
                </div>
                <div class="min-w-0 space-y-1">
                  <div>
                    <span class="inline-flex items-center gap-1.5 rounded-full bg-amber-400/15 border border-amber-300/35 px-2.5 sm:px-3 py-0.5 sm:py-1 text-xs sm:text-sm font-bold text-amber-200">
                      <Ticket class="size-3.5 text-amber-300 shrink-0" />
                      <span>Registration Form</span>
                    </span>
                  </div>
                  <p class="text-xs sm:text-sm font-medium text-blue-200/90 truncate">
                    Quanta Reunion Program
                  </p>
                </div>
              </div>
              
              <h1 class="text-2xl font-extrabold tracking-tight text-white sm:text-4xl lg:text-5xl sm:leading-tight">
                ঘরে ফেরা আনন্দ উৎসব ২০২৭
              </h1>

              <p class="mt-2.5 text-sm sm:text-base leading-relaxed text-blue-100/90">
                পাহাড়ের কোল ঘেঁষা প্রিয় ক্যাম্পাস কোয়ান্টামমে আবারও একত্রিত হওয়ার মহোৎসব। প্রাক্তন সকল শিক্ষার্থীর জন্য আনুষ্ঠানিক অনলাইন রেজিস্ট্রেশন ফরম।
              </p>

              <!-- Event Highlights in One Row (Mobile & Desktop) -->
              <div class="mt-4 flex flex-nowrap items-center gap-2 overflow-x-auto no-scrollbar py-0.5">
                <span class="inline-flex shrink-0 items-center gap-1.5 rounded-lg bg-[#0a234c]/85 border border-blue-400/20 px-2.5 sm:px-3 py-1.5 text-xs sm:text-sm font-medium text-blue-100 whitespace-nowrap">
                  <Calendar class="size-3.5 text-amber-400 shrink-0" /> ১৬-১৭ ডিসেম্বর ২০২৬
                </span>
                <span class="inline-flex shrink-0 items-center gap-1.5 rounded-lg bg-[#0a234c]/85 border border-blue-400/20 px-2.5 sm:px-3 py-1.5 text-xs sm:text-sm font-medium text-blue-100 whitespace-nowrap">
                  <MapPin class="size-3.5 text-rose-400 shrink-0" /> কোয়ান্টামম, লামা, বান্দরবান
                </span>
              </div>
            </div>

            <!-- Price & Form Status Box -->
            <div class="flex flex-col items-start rounded-sm bg-gradient-to-b from-[#0a234c]/90 to-[#061836]/90 border border-blue-400/25 p-2 lg:items-end lg:text-right">
              <span class="mt-2 text-xs sm:text-sm font-medium text-blue-200">Registration Fee</span>
              <div class="mt-0.5 flex items-baseline gap-1 text-3xl sm:text-4xl font-extrabold text-white">
                <span>৳১০০</span>
              </div>
            </div>
          </div>
        </section>

        <!-- Main Form Grid with Sticky Sidebar -->
        <form class="grid items-start gap-6 lg:grid-cols-[1fr_350px] xl:grid-cols-[1fr_380px]" @submit.prevent="completeRegistration">
          <div class="space-y-6">

            <!-- STEP 1: PERSONAL INFORMATION -->
            <section class="rounded-2xl bg-white p-5 sm:p-7">
              <div class="mb-5 flex items-center justify-between pb-3 border-b border-slate-100">
                <div class="flex items-center gap-3">
                  <div class="flex size-9 sm:size-10 shrink-0 items-center justify-center rounded-lg bg-blue-50 text-blue-700">
                    <User class="size-4.5" />
                  </div>
                  <div class="min-w-0">
                    <h2 class="text-base sm:text-lg font-bold text-[#0c234a] truncate">
                      Personal Details
                    </h2>
                  </div>
                </div>
              </div>

              <div class="grid gap-4 sm:grid-cols-2">
                <!-- Row 1: Full Name & Mobile Number -->
                <!-- Full Name -->
                <div>
                  <label class="mb-1.5 block text-sm font-semibold text-slate-800">
                    Full Name <span class="text-rose-500">*</span>
                  </label>
                  <div class="relative">
                    <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-blue-600">
                      <User class="size-4.5" />
                    </div>
                    <input
                      v-model.trim="form.fullName"
                      type="text"
                      required
                      class="block w-full min-h-[48px] rounded-xl border border-slate-200 bg-slate-50/60 py-3 pl-11 pr-4 text-base sm:text-sm text-[#0f2747] placeholder-slate-400 transition focus:border-blue-600 focus:bg-white focus:outline-none"
                    />
                  </div>
                </div>

                <!-- Mobile Number -->
                <div>
                  <div class="mb-1.5 flex items-center justify-between">
                    <label class="block text-sm font-semibold text-slate-800">
                      Mobile Number <span class="text-rose-500">*</span>
                    </label>
                    <span v-if="isMobileValid" class="flex items-center gap-1 text-xs font-semibold text-emerald-600">
                      <CheckCircle2 class="size-4" /> Valid
                    </span>
                  </div>
                  <div class="relative">
                    <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-emerald-600">
                      <Phone class="size-4.5" />
                    </div>
                    <input
                      v-model.trim="form.mobileNumber"
                      type="tel"
                      inputmode="tel"
                      required
                      pattern="01[3-9][0-9]{8}"
                      title="১১ ডিজিটের বাংলাদেশি মোবাইল নম্বর দিন, যেমন: 01XXXXXXXXX"
                      placeholder="01XXXXXXXXX"
                      class="block w-full min-h-[48px] rounded-xl border border-slate-200 bg-slate-50/60 py-3 pl-11 pr-4 text-base sm:text-sm text-[#0f2747] placeholder-slate-400 transition focus:border-blue-600 focus:bg-white focus:outline-none"
                    />
                  </div>
                </div>

                                <!-- Row 3: Quanta ID & Batch -->
                <!-- Quanta ID -->
                <div>
                  <label class="mb-1.5 block text-sm font-semibold text-slate-800">
                    Quanta ID <span class="text-rose-500">*</span>
                  </label>
                  <div class="relative">
                    <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-indigo-600">
                      <IdCard class="size-4.5" />
                    </div>
                    <input
                      v-model.trim="form.quantaId"
                      type="text"
                      required
                      class="block w-full min-h-[48px] rounded-xl border border-slate-200 bg-slate-50/60 py-3 pl-11 pr-4 text-base sm:text-sm text-[#0f2747] placeholder-slate-400 transition focus:border-blue-600 focus:bg-white focus:outline-none"
                    />
                  </div>
                </div>

                <!-- Batch / Passing Year -->
                <div>
                  <label class="mb-1.5 block text-sm font-semibold text-slate-800">
                    ব্যাচ বা পাসের বছর (batch / year)
                  </label>
                  <div class="relative">
                    <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-amber-600">
                      <GraduationCap class="size-4.5" />
                    </div>
                    <input
                      v-model.trim="form.batch"
                      type="text"
                      placeholder="যেমন: Batch 10 / HSC 2022"
                      class="block w-full min-h-[48px] rounded-xl border border-slate-200 bg-slate-50/60 py-3 pl-11 pr-4 text-base sm:text-sm text-[#0f2747] placeholder-slate-400 transition focus:border-blue-600 focus:bg-white focus:outline-none"
                    />
                  </div>
                </div>

                <!-- Row 2: Gender & T-Shirt Size -->
                <!-- Gender Selector with PROPER RADIO BUTTONS -->
                <div>
                  <label class="mb-1.5 block text-sm font-semibold text-slate-800">
                    Gender <span class="text-rose-500">*</span>
                  </label>
                  <div class="grid grid-cols-2 gap-2.5">
                    <!-- Male Radio Button -->
                    <label
                      class="relative flex min-h-[48px] cursor-pointer items-center justify-center sm:justify-start gap-3 rounded-xl border p-3 transition select-none"
                      :class="form.gender === 'male' ? 'border-blue-600 bg-blue-50/80 font-bold' : 'border-slate-200 bg-white hover:border-slate-300'"
                    >
                      <input
                        type="radio"
                        name="main_gender"
                        value="male"
                        v-model="form.gender"
                        class="sr-only"
                      />
                      <div
                        class="flex size-5 shrink-0 items-center justify-center rounded-full border-2 transition"
                        :class="form.gender === 'male' ? 'border-blue-600 bg-blue-600' : 'border-slate-300 bg-white'"
                      >
                        <div v-if="form.gender === 'male'" class="size-2 rounded-full bg-white"></div>
                      </div>
                      <span class="text-sm font-semibold text-[#0f2747]">
                        Male
                      </span>
                    </label>

                    <!-- Female Radio Button -->
                    <label
                      class="relative flex min-h-[48px] cursor-pointer items-center justify-center sm:justify-start gap-3 rounded-xl border p-3 transition select-none"
                      :class="form.gender === 'female' ? 'border-blue-600 bg-blue-50/80 font-bold' : 'border-slate-200 bg-white hover:border-slate-300'"
                    >
                      <input
                        type="radio"
                        name="main_gender"
                        value="female"
                        v-model="form.gender"
                        class="sr-only"
                      />
                      <div
                        class="flex size-5 shrink-0 items-center justify-center rounded-full border-2 transition"
                        :class="form.gender === 'female' ? 'border-blue-600 bg-blue-600' : 'border-slate-300 bg-white'"
                      >
                        <div v-if="form.gender === 'female'" class="size-2 rounded-full bg-white"></div>
                      </div>
                      <span class="text-sm font-semibold text-[#0f2747]">
                        Female
                      </span>
                    </label>
                  </div>
                </div>

                <!-- T-Shirt Size SELECT OPTION DROPDOWN LIST -->
                <div>
                  <div class="mb-1.5 flex items-center justify-between">
                    <label class="block text-sm font-semibold text-slate-800">
                      T-Shirt Size <span class="text-rose-500">*</span>
                    </label>
                  </div>
                  <div class="relative">
                    <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-blue-600">
                      <Shirt class="size-4.5" />
                    </div>
                    <select
                      v-model="form.tShirtSize"
                      required
                      class="block w-full min-h-[48px] appearance-none rounded-xl border border-slate-200 bg-slate-50/60 py-3 pl-11 pr-10 text-base sm:text-sm text-[#0f2747] transition focus:border-blue-600 focus:bg-white focus:outline-none"
                    >
                      <option value="" disabled>Select T-Shirt Size</option>
                      <option v-for="s in tShirtSizes" :key="s.code" :value="s.code">
                        {{ s.label }}
                      </option>
                    </select>
                    <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center pr-3.5 text-slate-400">
                      <ChevronDown class="size-4" />
                    </div>
                  </div>
                </div>


                <!-- Row 4: Occupation & Institution -->
                <!-- Occupation -->
                <div>
                  <label class="mb-1.5 block text-sm font-semibold text-slate-800">
                    Occupation
                  </label>
                  <div class="relative">
                    <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-sky-600">
                      <Briefcase class="size-4.5" />
                    </div>
                    <input
                      v-model.trim="form.occupation"
                      type="text"
                      placeholder="e.g., Student / Teacher / Employee"
                      class="block w-full min-h-[48px] rounded-xl border border-slate-200 bg-slate-50/60 py-3 pl-11 pr-4 text-base sm:text-sm text-[#0f2747] placeholder-slate-400 transition focus:border-blue-600 focus:bg-white focus:outline-none"
                    />
                  </div>
                </div>

                <!-- Institution -->
                <div>
                  <label class="mb-1.5 block text-sm font-semibold text-slate-800">
                    Institution / Workplace
                  </label>
                  <div class="relative">
                    <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-violet-600">
                      <Building2 class="size-4.5" />
                    </div>
                    <input
                      v-model.trim="form.institution"
                      type="text"
                      placeholder="e.g., University, College or Workplace"
                      class="block w-full min-h-[48px] rounded-xl border border-slate-200 bg-slate-50/60 py-3 pl-11 pr-4 text-base sm:text-sm text-[#0f2747] placeholder-slate-400 transition focus:border-blue-600 focus:bg-white focus:outline-none"
                    />
                  </div>
                </div>
              </div>
            </section>


            <!-- STEP 2: FAMILY INFORMATION (SPOUSE & CHILDREN) -->
            <section class="rounded-2xl bg-white p-5 sm:p-7">
              <div class="mb-5 flex items-center justify-between pb-3 border-b border-slate-100">
                <div class="flex items-center gap-3">
                  <div class="flex size-9 sm:size-10 shrink-0 items-center justify-center rounded-lg bg-pink-50 text-pink-700">
                    <Heart class="size-4.5" />
                  </div>
                  <div>
                    <h2 class="text-base sm:text-lg font-bold text-[#0c234a]">
                      পরিবার সংক্রান্ত তথ্য <span class="text-xs sm:text-sm font-normal text-slate-500">· family information</span>
                    </h2>
                    <p class="text-xs sm:text-sm text-slate-600">
                      আপনার সাথে পরিবারের সদস্য থাকলে চিহ্নিত করুন
                    </p>
                  </div>
                </div>
              </div>

              <div class="space-y-4">
                <!-- Spouse Checkbox -->
                <label
                  class="relative flex cursor-pointer items-start gap-3.5 rounded-xl border p-4 transition select-none"
                  :class="form.hasSpouse ? 'border-blue-600 bg-blue-50/70 shadow-xs' : 'border-slate-200 bg-white hover:border-slate-300'"
                >
                  <div class="pt-0.5">
                    <input
                      type="checkbox"
                      v-model="form.hasSpouse"
                      class="sr-only"
                    />
                    <div
                      class="flex size-5.5 shrink-0 items-center justify-center rounded-md border-2 transition"
                      :class="form.hasSpouse ? 'border-blue-600 bg-blue-600 text-white' : 'border-slate-300 bg-white'"
                    >
                      <Check v-if="form.hasSpouse" class="size-4 stroke-[3]" />
                    </div>
                  </div>
                  <div class="min-w-0 flex-1">
                    <div class="flex flex-wrap items-center gap-2">
                      <span class="text-sm sm:text-base font-bold text-[#0c234a]">
                        Spouse (স্বামী/স্ত্রী) নিয়ে আনতে চাই
                      </span>
                      <span
                        v-if="form.hasSpouse"
                        class="inline-flex items-center rounded-full bg-blue-100 px-2.5 py-0.5 text-xs font-semibold text-blue-800"
                      >
                        ১ জন অন্তর্ভুক্ত
                      </span>
                    </div>
                    <p class="mt-0.5 text-xs sm:text-sm text-slate-500">
                      পুনর্মিলনীতে আপনার সাথে আপনার স্বামী অথবা স্ত্রী অংশগ্রহণ করবেন
                    </p>
                  </div>
                </label>

                <!-- Number of Children Field -->
                <div class="rounded-xl border border-slate-200/80 bg-slate-50/60 p-4">
                  <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3">
                    <div>
                      <div class="flex items-center gap-2">
                        <Baby class="size-4.5 text-amber-600" />
                        <label class="block text-sm sm:text-base font-bold text-[#0c234a]">
                          সন্তান সংখ্যা (number of children)
                        </label>
                      </div>
                      <p class="mt-0.5 text-xs text-slate-500">
                        আপনার সাথে আসা সন্তানদের সংখ্যা (না আসলে ০ রাখুন)
                      </p>
                    </div>

                    <div class="flex items-center gap-2 self-start sm:self-auto">
                      <button
                        type="button"
                        class="flex size-10 items-center justify-center rounded-xl border border-slate-300 bg-white text-slate-700 transition hover:bg-slate-100 active:scale-95 disabled:opacity-40 disabled:cursor-not-allowed"
                        :disabled="form.childrenCount <= 0"
                        @click="decrementChildren"
                        aria-label="Decrease children count"
                      >
                        <Minus class="size-4.5" />
                      </button>

                      <div class="relative w-20">
                        <input
                          v-model.number="form.childrenCount"
                          type="number"
                          min="0"
                          max="20"
                          class="block w-full min-h-[42px] rounded-xl border border-slate-300 bg-white py-2 text-center text-base font-bold text-[#0c234a] focus:border-blue-600 focus:outline-none"
                        />
                      </div>

                      <button
                        type="button"
                        class="flex size-10 items-center justify-center rounded-xl border border-slate-300 bg-white text-slate-700 transition hover:bg-slate-100 active:scale-95"
                        @click="incrementChildren"
                        aria-label="Increase children count"
                      >
                        <Plus class="size-4.5" />
                      </button>
                    </div>
                  </div>
                </div>
              </div>
            </section>

            <!-- STEP 3: TRANSPORTATION (যাতায়াত ব্যবস্থা) -->
            <section class="rounded-2xl bg-white p-5 sm:p-7">
              <div class="mb-5 flex flex-wrap items-center justify-between gap-2 pb-3 border-b border-slate-100">
                <div class="flex items-center gap-3">
                  <div class="flex size-9 sm:size-10 shrink-0 items-center justify-center rounded-lg bg-blue-50 text-blue-700">
                    <Bus class="size-4.5" />
                  </div>
                  <div>
                    <h2 class="text-base sm:text-lg font-bold text-[#0c234a]">
                      যাতায়াত ব্যবস্থা <span class="text-xs sm:text-sm font-normal text-slate-500">· transportation</span>
                    </h2>
                    <p class="text-xs sm:text-sm text-slate-600">
                      অনুষ্ঠানে অংশগ্রহণের জন্য আপনার সুবিধাজনক যাতায়াত মাধ্যম বেছে নিন
                    </p>
                  </div>
                </div>

                <span class="rounded-full bg-blue-50 px-3 py-1 text-xs sm:text-sm font-semibold text-blue-800">
                  {{ selectedTransport.feeText }}
                </span>
              </div>

              <!-- Transport Options List -->
              <div class="space-y-3">
                <label
                  v-for="opt in transportOptions"
                  :key="opt.id"
                  class="relative flex cursor-pointer items-center justify-between gap-3.5 rounded-xl border p-4 transition select-none"
                  :class="form.transportOption === opt.id ? 'border-blue-600 bg-blue-50/70 shadow-xs ring-1 ring-blue-600/20' : 'border-slate-200 bg-white hover:border-slate-300'"
                >
                  <div class="flex items-center gap-3.5 min-w-0">
                    <!-- Radio circle -->
                    <input
                      type="radio"
                      name="transportOption"
                      :value="opt.id"
                      v-model="form.transportOption"
                      class="sr-only"
                    />
                    <div
                      class="flex size-5 shrink-0 items-center justify-center rounded-full border-2 transition"
                      :class="form.transportOption === opt.id ? 'border-blue-600 bg-blue-600' : 'border-slate-300 bg-white'"
                    >
                      <div v-if="form.transportOption === opt.id" class="size-2 rounded-full bg-white"></div>
                    </div>

                    <!-- Icon & Details -->
                    <div class="min-w-0">
                      <div class="flex flex-wrap items-center gap-2">
                        <span class="text-sm sm:text-base font-bold text-[#0c234a]">
                          {{ opt.title }}
                        </span>
                      </div>
                      <p class="mt-0.5 text-xs text-slate-500">
                        {{ opt.description }}
                      </p>
                    </div>
                  </div>

                  <!-- Fee Badge -->
                  <div class="text-right shrink-0">
                    <span
                      class="inline-block rounded-lg px-2.5 py-1 text-xs sm:text-sm font-bold"
                      :class="form.transportOption === opt.id ? 'bg-blue-600 text-white' : 'bg-slate-100 text-slate-700'"
                    >
                      {{ opt.badge }}
                    </span>
                  </div>
                </label>
              </div>
            </section>


            <!-- STEP 3: PAYMENT DETAILS -->
            <section class="rounded-2xl bg-white p-5 sm:p-7">
              <div class="mb-5 flex items-center justify-between pb-3 border-b border-slate-100">
                <div class="flex items-center gap-3">
                  <div class="flex size-9 sm:size-10 shrink-0 items-center justify-center rounded-lg bg-emerald-50 text-emerald-700">
                    <CreditCard class="size-4.5" />
                  </div>
                  <div>
                    <h2 class="text-base sm:text-lg font-bold text-[#0c234a]">
                      Payment Details
                    </h2>
                    <p class="text-xs sm:text-sm text-slate-600">Bkash, Nagad, Rocket, Bank Transfer or Cash</p>
                  </div>
                </div>
                <div class="text-right">
                  <span class="text-xs sm:text-sm text-slate-500">মোট প্রদেয়:</span>
                  <span class="ml-1 text-base sm:text-lg font-bold text-blue-800">৳{{ totalFee }}</span>
                </div>
              </div>

              <!-- Payment Method Selector -->
              <div>
                <label class="mb-2.5 block text-sm font-semibold text-slate-800">
                  Select Payment Method <span class="text-rose-500">*</span>
                </label>
                <div class="grid grid-cols-2 gap-2.5 sm:grid-cols-3 lg:grid-cols-5">
                  <button
                    v-for="(pm, idx) in paymentMethods"
                    :key="pm.id"
                    type="button"
                    class="relative flex min-h-[76px] flex-col items-center justify-center rounded-xl border p-2 text-center transition"
                    :class="[
                      form.paymentMethod === pm.id ? pm.activeBg + ' font-bold' : 'border-slate-200 bg-white text-slate-600 hover:border-slate-300',
                      idx === 4 ? 'col-span-2 sm:col-span-1' : ''
                    ]"
                    @click="form.paymentMethod = pm.id"
                  >
                    <!-- Brand Icon Graphic -->
                    <div class="mb-1 flex size-8 items-center justify-center">
                      <!-- bKash Origami Bird SVG -->
                      <svg v-if="pm.id === 'bkash'" viewBox="0 0 100 100" class="size-8" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <path d="M12 48L42 16L72 48L52 88L32 72L12 48Z" fill="#E2136E"/>
                        <path d="M42 16L82 28L72 48L42 16Z" fill="#D12053"/>
                        <path d="M72 48L92 42L78 64L72 48Z" fill="#B31A45"/>
                        <path d="M52 88L68 96L78 64L52 88Z" fill="#E2136E"/>
                      </svg>

                      <!-- Nagad Swirl/Flame SVG -->
                      <svg v-else-if="pm.id === 'nagad'" viewBox="0 0 100 100" class="size-8" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <circle cx="50" cy="50" r="44" fill="#FFF2E8"/>
                        <path d="M50 16C36 28 32 44 38 56C42 64 50 68 56 64C64 60 66 48 58 40C52 34 50 26 50 16Z" fill="#F7941D"/>
                        <path d="M54 36C48 42 46 52 50 58C54 62 60 62 64 58C68 54 68 46 62 42C58 38 56 34 54 36Z" fill="#ED1C24"/>
                      </svg>

                      <!-- Rocket DBBL Flyer SVG -->
                      <svg v-else-if="pm.id === 'rocket'" viewBox="0 0 100 100" class="size-8" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <circle cx="50" cy="50" r="44" fill="#F6EBF8"/>
                        <path d="M50 14L66 44H34L50 14Z" fill="#8C3494"/>
                        <path d="M34 44H66V68C66 74 60 80 50 82C40 80 34 74 34 68V44Z" fill="#75227C"/>
                        <circle cx="50" cy="54" r="7" fill="#FFFFFF"/>
                        <path d="M42 82L50 94L58 82H42Z" fill="#ED1C24"/>
                      </svg>

                      <!-- Bank Transfer Building SVG -->
                      <svg v-else-if="pm.id === 'bank'" viewBox="0 0 100 100" class="size-8" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <circle cx="50" cy="50" r="44" fill="#EFF6FF"/>
                        <path d="M50 18L18 34V40H82V34L50 18Z" fill="#1D4ED8"/>
                        <rect x="24" y="44" width="8" height="28" rx="2" fill="#2563EB"/>
                        <rect x="38" y="44" width="8" height="28" rx="2" fill="#2563EB"/>
                        <rect x="54" y="44" width="8" height="28" rx="2" fill="#2563EB"/>
                        <rect x="68" y="44" width="8" height="28" rx="2" fill="#2563EB"/>
                        <rect x="16" y="74" width="68" height="8" rx="2" fill="#1D4ED8"/>
                      </svg>

                      <!-- Cash / Spot Banknote SVG -->
                      <svg v-else viewBox="0 0 100 100" class="size-8" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <circle cx="50" cy="50" r="44" fill="#ECFDF5"/>
                        <rect x="16" y="26" width="68" height="42" rx="6" fill="#059669"/>
                        <rect x="22" y="32" width="56" height="30" rx="4" stroke="#A7F3D0" stroke-width="2"/>
                        <circle cx="50" cy="47" r="8" fill="#A7F3D0"/>
                        <path d="M30 68L70 68L80 76L20 76L30 68Z" fill="#047857"/>
                      </svg>
                    </div>

                    <span class="text-sm font-bold leading-tight">{{ pm.name }}</span>
                    <span class="text-xs text-slate-500">{{ pm.bnName }}</span>

                    <!-- Checked indicator -->
                    <span
                      v-if="form.paymentMethod === pm.id"
                      class="absolute -top-1 -right-1 flex size-4.5 items-center justify-center rounded-full bg-blue-700 text-xs text-white"
                    >
                      ✓
                    </span>
                  </button>
                </div>
              </div>

              <!-- Dynamic Instruction Box -->
              <div class="mt-4 rounded-xl bg-blue-50/60 p-4">
                <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3">
                  <div>
                    <span class="text-sm font-bold text-blue-900">
                      {{ selectedPaymentConfig.name }} পেমেন্ট সংক্রান্ত তথ্য
                    </span>
                    <div class="mt-1 flex flex-wrap items-center gap-2.5">
                      <span class="font-mono text-lg sm:text-xl font-bold text-blue-950">
                        {{ selectedPaymentConfig.number }}
                      </span>
                      <span class="rounded bg-blue-100 px-2.5 py-0.5 text-xs text-blue-800 font-semibold">
                        {{ selectedPaymentConfig.type }}
                      </span>
                    </div>
                  </div>

                  <button
                    v-if="form.paymentMethod !== 'cash'"
                    type="button"
                    class="inline-flex min-h-[44px] items-center justify-center gap-2 rounded-xl bg-white px-4 py-2.5 text-xs sm:text-sm font-semibold text-blue-800 transition hover:bg-blue-100 active:scale-95 border border-slate-200 w-full sm:w-auto"
                    @click="copyPaymentNumber(selectedPaymentConfig.number)"
                  >
                    <Check v-if="copied" class="size-4 text-emerald-600" />
                    <Copy v-else class="size-4" />
                    <span>{{ copied ? 'কপি হয়েছে! ✓' : 'নম্বর কপি করুন' }}</span>
                  </button>
                </div>

                <div class="mt-3 flex items-start gap-2 border-t border-slate-200/60 pt-2.5 text-xs sm:text-sm text-slate-700 leading-relaxed">
                  <Info class="size-4.5 shrink-0 text-blue-700 mt-0.5" />
                  <p>
                    <strong class="text-blue-950">নির্দেশনা:</strong> {{ selectedPaymentConfig.instructions }} মোট প্রদেয় পরিমাণ <strong>৳{{ totalFee }}</strong> টাকা। ফি পাঠানোর পর নিচের ঘরে ট্রানজেকশন নম্বর (Transaction Number) দিন।
                  </p>
                </div>
              </div>

              <!-- Transaction Number Only -->
              <div class="mt-4">
                <label class="mb-1.5 block text-sm font-semibold text-slate-800">
                  Transaction Number <span class="text-rose-500">*</span>
                </label>
                <div class="relative">
                  <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-indigo-600">
                    <CreditCard class="size-4.5" />
                  </div>
                  <input
                    v-model.trim="form.transactionId"
                    type="text"
                    required
                    class="block w-full min-h-[48px] rounded-xl border border-slate-200 bg-slate-50/60 py-3 pl-11 pr-4 text-base sm:text-sm font-mono text-[#0f2747] placeholder-slate-400 transition focus:border-blue-600 focus:bg-white focus:outline-none"
                  />
                </div>
                <p class="mt-1 text-xs text-slate-500">
                  পেমেন্ট সম্পন্ন করার পর প্রাপ্ত ট্রানজেকশন নম্বর বা আইডিটি এখানে লিখুন।
                </p>
              </div>
            </section>
          </div>

          <!-- SIDEBAR: REGISTRATION SUMMARY & SUBMISSION -->
          <aside class="space-y-4 lg:sticky lg:top-6">
            <div class="rounded-2xl bg-white p-5 border border-slate-200/60">
              <div class="flex items-center justify-between pb-3 border-b border-slate-100">
                <span class="inline-flex items-center gap-1.5 text-sm sm:text-base font-bold text-blue-950">
                  <CreditCard class="size-4.5 text-blue-700" />Registration Summary
                </span>
                <span class="rounded-full bg-emerald-50 px-2.5 py-0.5 text-xs font-semibold text-emerald-700">
                  নিবন্ধন ফি ৳১০০
                </span>
              </div>

              <!-- Price Breakdown -->
              <div class="mt-4 space-y-2.5 border-b border-slate-100 pb-3.5 text-sm">
                <div class="flex items-center justify-between text-slate-700">
                  <span>নিবন্ধন ফি (registration fee)</span>
                  <span class="font-bold text-slate-900">৳{{ registrationFee }}</span>
                </div>

                <div class="flex items-center justify-between text-slate-700">
                  <div class="min-w-0 pr-2">
                    <span class="block font-medium">যাতায়াত ব্যবস্থা</span>
                    <span class="block text-xs text-slate-500 truncate">{{ selectedTransport.title }}</span>
                  </div>
                  <span class="font-bold shrink-0" :class="selectedTransport.fee > 0 ? 'text-blue-900' : 'text-slate-800'">
                    {{ selectedTransport.fee > 0 ? '৳' + selectedTransport.fee : '০ টাকা' }}
                  </span>
                </div>

                <div v-if="form.hasSpouse || form.childrenCount > 0" class="rounded-lg bg-slate-50 p-2.5 text-xs text-slate-600 space-y-1">
                  <div v-if="form.hasSpouse" class="flex justify-between">
                    <span>স্পাউস (স্বামী/স্ত্রী):</span>
                    <span class="font-semibold text-slate-800">১ জন</span>
                  </div>
                  <div v-if="form.childrenCount > 0" class="flex justify-between">
                    <span>সন্তান:</span>
                    <span class="font-semibold text-slate-800">{{ form.childrenCount }} জন</span>
                  </div>
                </div>

                <div class="flex items-center justify-between text-slate-500 text-xs sm:text-sm">
                  <span>মোট সদস্য সংখ্যা</span>
                  <span class="font-semibold text-slate-800">{{ attendeeCount }} জন</span>
                </div>
              </div>

              <!-- Total Payable -->
              <div class="mt-3.5 flex items-baseline justify-between">
                <div>
                  <span class="text-xs sm:text-sm text-slate-500">সর্বমোট প্রদেয় ফি</span>
                  <p class="text-xs font-semibold text-blue-800">total registration fee</p>
                </div>
                <div class="text-right">
                  <span class="text-2xl sm:text-3xl font-black text-blue-950">৳{{ totalFee }}</span>
                </div>
              </div>

              <!-- Submit Button -->
              <button
                type="submit"
                :disabled="isSubmitting"
                class="mt-4 flex w-full min-h-[50px] items-center justify-center gap-2 rounded-xl bg-blue-700 px-5 py-3 text-center text-sm sm:text-base font-bold text-white transition hover:bg-blue-800 active:scale-[0.99] disabled:opacity-60 disabled:cursor-not-allowed"
              >
                <span>{{ isSubmitting ? 'Processing...' : 'Complete Registration' }}</span>
                <ArrowRight class="size-4.5" />
              </button>

              <div class="mt-3 flex items-start gap-2 rounded-lg bg-blue-50/70 p-2.5 text-xs text-blue-900">
                <Info class="size-4 shrink-0 text-blue-700 mt-0.5" />
                <p>
                  ফরমটি জমা দেওয়ার পর আমাদের টিম অতি দ্রুত কল করে পেমেন্ট প্রাপ্তি নিশ্চিত করবেন।
                </p>
              </div>
            </div>

            <!-- Help / Hotline Card -->
            <div class="rounded-xl bg-white p-4 text-xs sm:text-sm">
              <div class="flex items-start gap-3">
                <div class="flex size-8 shrink-0 items-center justify-center rounded-lg bg-blue-50 text-blue-700">
                  <HelpCircle class="size-4.5" />
                </div>
                <div>
                  <p class="font-bold text-sm text-[#0c234a]">সহায়তা হেল্পলাইন (helpline)</p>
                  <p class="mt-0.5 text-xs sm:text-sm text-slate-600">
                    নিবন্ধন বা যাতায়াত সংক্রান্ত কোনো প্রশ্নে:
                  </p>
                  <p class="mt-1 font-mono font-bold text-sm sm:text-base text-blue-800">
                    📞 01700-000000 / 01800-000000
                  </p>
                </div>
              </div>
            </div>
          </aside>
        </form>

        <!-- STICKY MOBILE BOTTOM BAR (FOR SCREENS < LG) -->
        <div class="lg:hidden fixed bottom-0 left-0 right-0 z-40 bg-white border-t border-slate-200 px-4 py-3 flex items-center justify-between gap-3">
          <div>
            <span class="text-xs text-slate-500 block leading-tight">সর্বমোট ফি (নিবন্ধন + যাতায়াত)</span>
            <span class="text-xl font-black text-blue-900 leading-none">৳{{ totalFee }}</span>
          </div>

          <button
            type="button"
            :disabled="isSubmitting"
            class="flex min-h-[46px] items-center gap-2 rounded-xl bg-blue-700 px-5 py-2.5 text-sm font-bold text-white transition hover:bg-blue-800 active:scale-95 disabled:opacity-60 disabled:cursor-not-allowed"
            @click="completeRegistration"
          >
            <span>{{ isSubmitting ? 'জমা হচ্ছে...' : 'নিবন্ধন জমা দিন' }}</span>
            <ArrowRight class="size-4" />
          </button>
        </div>
      </template>
    </div>
  </div>
</template>
