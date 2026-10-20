<script setup>
import { computed, reactive, ref } from 'vue'
import confetti from 'canvas-confetti'
import {
  User,
  Phone,
  Check,
  CheckCircle2,
  Calendar,
  Copy,
  Plus,
  Trash2,
  CreditCard,
  Shirt,
  Building2,
  Briefcase,
  IdCard,
  MapPin,
  PartyPopper,
  Users,
  Heart,
  Info,
  ShieldCheck,
  Ticket,
  Printer,
  Share2,
  ArrowRight,
  HelpCircle,
  GraduationCap,
  ChevronDown,
  UserPlus,
  Smile,
  Handshake,
  UtensilsCrossed
} from '@lucide/vue'

const registrationFee = 100
const isComplete = ref(false)
const copied = ref(false)
const sameAsMyNumber = ref(false)
const activeStep = ref(1)
const registrationRefNumber = ref('')

let nextGuestId = 1

const tShirtSizes = [
  { code: 's', label: 'small (s)', chest: '36"' },
  { code: 'm', label: 'medium (m)', chest: '38"' },
  { code: 'l', label: 'large (l)', chest: '40"' },
  { code: 'xl', label: 'extra large (xl)', chest: '42"' },
  { code: 'xxl', label: '2xl (xxl)', chest: '44"' },
  { code: '3xl', label: '3xl (3xl)', chest: '46"' },
]

const paymentMethods = [
  {
    id: 'bkash',
    name: 'bKash',
    bnName: 'বিকাশ',
    number: '01712-345678',
    type: 'personal (send money)',
    brandColor: '#E2136E',
    activeBg: 'bg-pink-50 border-pink-500',
    instructions: 'বিকাশ অ্যাপ বা *247# ডায়াল করে send money করুন।'
  },
  {
    id: 'nagad',
    name: 'nagad',
    bnName: 'নগদ',
    number: '01812-345678',
    type: 'personal (send money)',
    brandColor: '#F7941D',
    activeBg: 'bg-orange-50 border-orange-500',
    instructions: 'নগদ অ্যাপ বা *167# ডায়াল করে send money করুন।'
  },
  {
    id: 'rocket',
    name: 'rocket',
    bnName: 'রকেট',
    number: '01912-345678-9',
    type: 'personal (send money)',
    brandColor: '#8C3494',
    activeBg: 'bg-purple-50 border-purple-500',
    instructions: 'রকেট অ্যাপ বা *322# ডায়াল করে send money করুন।'
  },
  {
    id: 'bank',
    name: 'bank transfer',
    bnName: 'ব্যাংক ট্রান্সফার',
    number: 'a/c: 123-456-78901 (city bank)',
    type: 'online / branch deposit',
    brandColor: '#1D4ED8',
    activeBg: 'bg-blue-50 border-blue-600',
    instructions: 'ব্যাংক একাউন্টে ফি জমা দিয়ে স্লিপ বা ট্রানজেকশন নম্বর লিখুন।'
  },
  {
    id: 'cash',
    name: 'cash / spot',
    bnName: 'স্পট পেমেন্ট',
    number: 'কোয়ান্টামম রেজিস্ট্রেশন বুথ',
    type: 'pay at entrance counter',
    brandColor: '#059669',
    activeBg: 'bg-emerald-50 border-emerald-600',
    instructions: 'অনুষ্ঠানের দিন কোয়ান্টামম এন্ট্রি বুথে সরাসরি ক্যাশ প্রদান করবেন।'
  }
]

const relations = [
  { code: 'spouse', label: 'স্বামী / স্ত্রী (spouse)', icon: 'heart' },
  { code: 'child', label: 'সন্তান (child)', icon: 'smile' },
  { code: 'parent', label: 'বাবা / মা (parent)', icon: 'users' },
  { code: 'sibling', label: 'ভাই / বোন (sibling)', icon: 'userplus' },
  { code: 'friend', label: 'সহপাঠী / বন্ধু (friend)', icon: 'handshake' },
  { code: 'other', label: 'অন্যান্য (other)', icon: 'user' }
]

const form = reactive({
  fullName: '',
  quantaId: '',
  gender: 'male',
  mobileNumber: '',
  occupation: '',
  institution: '',
  batch: '',
  tShirtSize: 'l',
  guests: [],
  paymentMethod: 'bkash',
  senderNumber: '',
  transactionId: '',
})

const attendeeCount = computed(() => form.guests.length + 1)
const totalFee = computed(() => registrationFee)

const selectedPaymentConfig = computed(() => {
  return paymentMethods.find((p) => p.id === form.paymentMethod) || paymentMethods[0]
})

const isMobileValid = computed(() => {
  return /^01[3-9]\d{8}$/.test(form.mobileNumber.replace(/\D/g, ''))
})

function addGuest() {
  form.guests.push({
    id: nextGuestId++,
    name: '',
    number: '',
    gender: 'male',
    relation: 'friend',
    tShirtSize: 'l'
  })
}

function removeGuest(id) {
  form.guests = form.guests.filter((guest) => guest.id !== id)
}

function toggleSameAsMyNumber() {
  sameAsMyNumber.value = !sameAsMyNumber.value
  if (sameAsMyNumber.value) {
    form.senderNumber = form.mobileNumber
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
  const randomSuffix = Math.floor(10000 + Math.random() * 90000)
  registrationRefNumber.value = `qcsc-2027-${randomSuffix}`

  isComplete.value = true
  window.scrollTo({ top: 0, behavior: 'smooth' })
  setTimeout(() => {
    triggerFestiveConfetti()
  }, 200)
}

function printTicket() {
  window.print()
}

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
</script>

<template>
  <div class="min-h-screen bg-[#f1f6fd] text-[#0f2747] selection:bg-blue-600 selection:text-white pb-20 lg:pb-0">
    <!-- Top Institutional Ribbon -->
    <header class="no-print bg-[#0d2347] text-blue-100">
      <div class="mx-auto flex max-w-7xl flex-wrap items-center justify-between gap-2 px-3.5 py-2.5 text-xs sm:px-6 lg:px-8">
        <div class="flex items-center gap-2 font-medium text-blue-100">
          <span class="flex size-6 shrink-0 items-center justify-center rounded-md bg-blue-600/60 text-white">
            <GraduationCap class="size-3.5 text-blue-200" />
          </span>
          <span class="truncate">কোয়ান্টাম কসমো স্কুল ও কলেজ</span>
          <span class="hidden text-blue-400 sm:inline">•</span>
          <span class="hidden text-blue-200 sm:inline">কোয়ান্টা রিইউনিয়ন</span>
        </div>
        <div class="hidden items-center gap-3 text-blue-200 md:flex">
          <span class="flex items-center gap-1.5"><Calendar class="size-3.5 text-amber-400" /> ১৬-১৭ ডিসেম্বর ২০২৬</span>
          <span class="text-blue-400">•</span>
          <span class="flex items-center gap-1.5"><MapPin class="size-3.5 text-rose-400" /> কোয়ান্টামম, লামা, বান্দরবান</span>
        </div>
        <div class="flex items-center gap-1.5 text-[11px] sm:text-xs font-semibold text-emerald-300">
          <span class="relative flex size-2">
            <span class="absolute inline-flex h-full w-full animate-ping rounded-full bg-emerald-400 opacity-75"></span>
            <span class="relative inline-flex size-2 rounded-full bg-emerald-400"></span>
          </span>
          <span>নিবন্ধন চলছে</span>
        </div>
      </div>
    </header>

    <div class="mx-auto max-w-7xl px-3 sm:px-6 sm:py-8 lg:px-8 py-4">
      
      <!-- SUCCESS SCREEN: PRINTABLE OFFICIAL PASS -->
      <section v-if="isComplete" class="mx-auto max-w-3xl py-2 sm:py-4">
        <div class="no-print text-center px-2">
          <div class="inline-flex items-center justify-center rounded-2xl bg-gradient-to-tr from-blue-600 to-indigo-600 p-3.5 sm:p-4 text-white">
            <PartyPopper class="size-8 sm:size-10" />
          </div>
          <span class="mt-3 block text-xs font-semibold text-blue-600">
            নিবন্ধন সফলভাবে সম্পন্ন হয়েছে • registration confirmed
          </span>
          <h1 class="mt-1.5 text-xl font-bold text-[#0c234a] sm:text-3xl lg:text-4xl">
            ঘরে ফেরা আনন্দ উৎসব ২০২৭-এ স্বাগতম
          </h1>
          <p class="mx-auto mt-2 max-w-xl text-xs sm:text-sm text-slate-600">
            প্রিয় <strong class="text-blue-900">{{ form.fullName }}</strong>, কোয়ান্টাম কসমো স্কুল ও কলেজ কোয়ান্টা রিইউনিয়ন অনুষ্ঠানে আপনার অংশগ্রহণ নিশ্চিত হয়েছে।
          </p>
        </div>

        <!-- PRINTABLE DELEGATE PASS -->
        <div class="ticket-card relative mt-5 rounded-2xl border border-blue-600 bg-white p-4 sm:p-7">
          <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3 border-b border-slate-100 pb-4">
            <div class="flex items-center gap-3">
              <div class="flex size-12 sm:size-14 shrink-0 items-center justify-center rounded-2xl bg-blue-700 text-lg sm:text-xl font-bold text-white">
                qc
              </div>
              <div class="min-w-0">
                <span class="inline-block rounded-full bg-blue-50 px-2 py-0.5 text-[10px] sm:text-xs font-semibold text-blue-700">
                  অফিসিয়াল ডেলিগেট পাস • delegate pass
                </span>
                <h2 class="text-lg font-bold text-[#0c234a] sm:text-xl truncate">
                  ঘরে ফেরা আনন্দ উৎসব ২০২৭
                </h2>
                <p class="text-[11px] sm:text-xs text-slate-500 truncate">
                  কোয়ান্টাম কসমো স্কুল ও কলেজ • কোয়ান্টা রিইউনিয়ন
                </p>
              </div>
            </div>

            <div class="rounded-xl bg-blue-50/70 px-3 py-2 text-left sm:text-right">
              <p class="text-[10px] sm:text-xs text-slate-500">রেফারেন্স নম্বর / ref no.</p>
              <p class="font-mono text-sm sm:text-base font-bold text-blue-900">{{ registrationRefNumber }}</p>
            </div>
          </div>

          <!-- Pass Details Grid -->
          <div class="grid gap-4 sm:gap-6 py-4 sm:py-5 sm:grid-cols-2">
            <div class="space-y-3">
              <div>
                <p class="text-[11px] sm:text-xs text-slate-500">অংশগ্রহণকারীর নাম (attendee)</p>
                <p class="text-sm sm:text-base font-bold text-[#0c234a]">{{ form.fullName }}</p>
              </div>

              <div class="grid grid-cols-2 gap-2 sm:gap-3">
                <div>
                  <p class="text-[11px] sm:text-xs text-slate-500">কোয়ান্টা আইডি / রোল</p>
                  <p class="font-mono text-xs sm:text-sm font-semibold text-blue-800">{{ form.quantaId }}</p>
                </div>
                <div>
                  <p class="text-[11px] sm:text-xs text-slate-500">টি-শার্ট সাইজ</p>
                  <p class="text-xs sm:text-sm font-semibold text-slate-800">{{ form.tShirtSize }}</p>
                </div>
              </div>

              <div class="grid grid-cols-2 gap-2 sm:gap-3">
                <div>
                  <p class="text-[11px] sm:text-xs text-slate-500">মোবাইল নম্বর</p>
                  <p class="font-mono text-xs sm:text-sm text-slate-800">{{ form.mobileNumber }}</p>
                </div>
                <div>
                  <p class="text-[11px] sm:text-xs text-slate-500">ব্যাচ / পাসের বছর</p>
                  <p class="text-xs sm:text-sm font-semibold text-slate-800 truncate">{{ form.batch || 'উদ্বৃত্ত নয়' }}</p>
                </div>
              </div>

              <div>
                <p class="text-[11px] sm:text-xs text-slate-500">বর্তমান প্রতিষ্ঠান / কর্মক্ষেত্র</p>
                <p class="truncate text-xs sm:text-sm text-slate-800">{{ form.institution || 'উদ্বৃত্ত নয়' }}</p>
              </div>
            </div>

            <!-- Right Column -->
            <div class="flex flex-col justify-between rounded-xl bg-blue-50/40 p-3.5 sm:p-4">
              <div class="space-y-2">
                <div class="flex items-center justify-between text-xs">
                  <span class="text-slate-600">মোট অংশগ্রহণকারী:</span>
                  <span class="rounded-full bg-blue-100 px-2 py-0.5 font-bold text-blue-800">
                    {{ attendeeCount }} জন
                  </span>
                </div>

                <div v-if="form.guests.length" class="border-t border-slate-200/60 pt-2 text-xs text-slate-600">
                  <span class="font-semibold text-slate-700">সঙ্গী / অতিথি:</span>
                  <ul class="mt-1 list-inside list-disc space-y-0.5 text-slate-700 text-[11px]">
                    <li v-for="g in form.guests" :key="g.id">
                      {{ g.name }} ({{ g.relation }}, সাইজ: {{ g.tShirtSize }})
                    </li>
                  </ul>
                </div>

                <div class="flex items-center justify-between border-t border-slate-200/60 pt-2 text-xs">
                  <span class="text-slate-600">পেমেন্ট স্ট্যাটাস:</span>
                  <span class="inline-flex items-center gap-1 font-bold text-emerald-700">
                    <CheckCircle2 class="size-3.5" /> পরিশোধিত (৳{{ totalFee }})
                  </span>
                </div>

                <div class="text-[11px] text-slate-500">
                  মাধ্যম: <strong class="text-slate-800">{{ form.paymentMethod }}</strong>
                  <span v-if="form.transactionId"> • TrxID: <span class="font-mono text-blue-900">{{ form.transactionId }}</span></span>
                </div>
              </div>

              <!-- Barcode Mockup -->
              <div class="mt-3 border-t border-slate-200/60 pt-2 text-center">
                <div class="mx-auto flex h-7 max-w-[170px] items-center justify-center space-x-1 opacity-75">
                  <div class="h-full w-1 bg-slate-800"></div>
                  <div class="h-full w-2 bg-slate-800"></div>
                  <div class="h-full w-0.5 bg-slate-800"></div>
                  <div class="h-full w-3 bg-slate-800"></div>
                  <div class="h-full w-1 bg-slate-800"></div>
                  <div class="h-full w-2 bg-slate-800"></div>
                  <div class="h-full w-1 bg-slate-800"></div>
                  <div class="h-full w-3 bg-slate-800"></div>
                  <div class="h-full w-1 bg-slate-800"></div>
                  <div class="h-full w-0.5 bg-slate-800"></div>
                </div>
                <p class="mt-1 font-mono text-[9px] text-slate-500">* {{ registrationRefNumber }} *</p>
              </div>
            </div>
          </div>

          <!-- Pass Footer -->
          <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-1.5 border-t border-slate-100 pt-3 text-[11px] sm:text-xs text-slate-600">
            <span class="flex items-center gap-1.5 font-medium text-blue-900">
              <Calendar class="size-3.5 text-blue-600 shrink-0" /> ১৬-১৭ ডিসেম্বর ২০২৬
            </span>
            <span class="flex items-center gap-1.5 font-medium text-blue-900">
              <MapPin class="size-3.5 text-blue-600 shrink-0" /> কোয়ান্টামম, লামা, বান্দরবান
            </span>
          </div>
        </div>

        <!-- Action Buttons (Mobile Friendly Full-Width Stacking) -->
        <div class="no-print mt-5 flex flex-col sm:flex-row items-center justify-center gap-2.5">
          <button
            type="button"
            class="flex w-full sm:w-auto items-center justify-center gap-2 rounded-xl bg-blue-700 px-5 py-3 text-xs sm:text-sm font-semibold text-white transition hover:bg-blue-800"
            @click="printTicket"
          >
            <Printer class="size-4" />
            <span>পাস প্রিন্ট বা সেভ করুন (print / save pass)</span>
          </button>

          <button
            type="button"
            class="flex w-full sm:w-auto items-center justify-center gap-2 rounded-xl bg-white px-4 py-3 text-xs sm:text-sm font-medium text-blue-900 transition hover:bg-blue-50 border border-slate-200"
            @click="addToGoogleCalendar"
          >
            <Calendar class="size-4 text-blue-600" />
            <span>ক্যালেন্ডারে যোগ করুন</span>
          </button>

          <button
            type="button"
            class="flex w-full sm:w-auto items-center justify-center gap-2 rounded-xl bg-emerald-50 px-4 py-3 text-xs sm:text-sm font-medium text-emerald-800 transition hover:bg-emerald-100 border border-emerald-200"
            @click="shareViaWhatsApp"
          >
            <Share2 class="size-4" />
            <span>হোয়াটসঅ্যাপে শেয়ার করুন</span>
          </button>
        </div>

        <div class="no-print mt-5 text-center">
          <button
            type="button"
            class="text-xs font-semibold text-blue-700 underline decoration-blue-300 underline-offset-4 hover:text-blue-900"
            @click="isComplete = false"
          >
            ← তথ্য পরিবর্তন বা নতুন কারও নিবন্ধন করুন
          </button>
        </div>
      </section>

      <!-- REGISTRATION FORM SECTION -->
      <template v-else>
        <!-- Blue Hero Section -->
        <section class="relative mb-5 sm:mb-6 rounded-2xl bg-gradient-to-br from-[#0c234a] via-[#103369] to-[#0d2754] p-4 sm:p-7 text-white">
          <div class="grid gap-5 lg:grid-cols-[1fr_auto] lg:items-center">
            <div class="max-w-3xl">
              <div class="mb-2.5 inline-flex items-center gap-1.5 rounded-full bg-blue-500/20 px-3 py-1 text-[11px] sm:text-xs font-semibold text-blue-200">
                <GraduationCap class="size-3.5 text-blue-300 shrink-0" />
                <span>কোয়ান্টাম কসমো স্কুল ও কলেজ • কোয়ান্টা রিইউনিয়ন</span>
              </div>
              
              <h1 class="text-2xl font-extrabold tracking-tight text-white sm:text-4xl lg:text-5xl sm:leading-tight">
                ঘরে ফেরা আনন্দ উৎসব ২০২৭
              </h1>

              <p class="mt-2 text-xs sm:text-sm leading-relaxed text-blue-100">
                পাহাড়ের কোল ঘেঁষা প্রিয় ক্যাম্পাস কোয়ান্টামমে আবারও একত্রিত হওয়ার মহোৎসব। সহপাঠী, শিক্ষক ও আপনজনদের সাথে সুন্দর স্মৃতি রোমন্থনে অংশ নিন।
              </p>

              <!-- Event Highlights Responsive Wrap -->
              <div class="mt-4 flex flex-wrap gap-1.5 sm:gap-2 text-[11px] sm:text-xs">
                <span class="inline-flex items-center gap-1 rounded-lg bg-blue-900/40 px-2.5 py-1 text-blue-100">
                  <Calendar class="size-3 text-amber-400 shrink-0" /> ১৬-১৭ ডিসেম্বর ২০২৬
                </span>
                <span class="inline-flex items-center gap-1 rounded-lg bg-blue-900/40 px-2.5 py-1 text-blue-100">
                  <MapPin class="size-3 text-rose-400 shrink-0" /> কোয়ান্টামম, লামা, বান্দরবান
                </span>
                <span class="inline-flex items-center gap-1 rounded-lg bg-blue-900/40 px-2.5 py-1 text-blue-100">
                  <Shirt class="size-3 text-blue-300 shrink-0" /> উৎসব টি-শার্ট ও উপহার কিট
                </span>
                <span class="inline-flex items-center gap-1 rounded-lg bg-blue-900/40 px-2.5 py-1 text-blue-100">
                  <UtensilsCrossed class="size-3 text-amber-300 shrink-0" /> গ্র্যান্ড ভোজ ও সাংস্কৃতিক সন্ধ্যা
                </span>
              </div>
            </div>

            <!-- Price Highlight Box -->
            <div class="flex flex-col items-start rounded-xl bg-blue-900/40 p-4 sm:p-5 lg:items-end lg:text-right">
              <span class="text-[11px] sm:text-xs font-medium text-blue-200">নিবন্ধন ফি (registration fee)</span>
              <div class="mt-0.5 flex items-baseline gap-1 text-2xl sm:text-3xl lg:text-4xl font-bold text-white">
                <span>৳১০০</span>
                <span class="text-xs font-normal text-blue-200">/ ফিক্সড ফি</span>
              </div>
              <p class="mt-1 text-[11px] sm:text-xs text-blue-200">
                অতিথি সহ সকল আয়োজন ও ভোজ অন্তর্ভুক্ত
              </p>
              <div class="mt-2.5 inline-flex items-center gap-1 text-[11px] font-medium text-emerald-300">
                <ShieldCheck class="size-3.5 shrink-0" /> ১০০% সুরক্ষিত অনলাইন নিবন্ধন
              </div>
            </div>
          </div>
        </section>

        <!-- Main Form Grid with Sticky Sidebar -->
        <form class="grid items-start gap-5 sm:gap-6 lg:grid-cols-[1fr_350px] xl:grid-cols-[1fr_380px]" @submit.prevent="completeRegistration">
          <div class="space-y-5 sm:space-y-6">

            <!-- STEP 1: PERSONAL INFORMATION -->
            <section class="rounded-2xl bg-white p-4 sm:p-6 lg:p-7">
              <div class="mb-4 sm:mb-5 flex items-center justify-between pb-2.5 border-b border-slate-100">
                <div class="flex items-center gap-2.5">
                  <div class="flex size-8 sm:size-9 shrink-0 items-center justify-center rounded-lg bg-blue-50 text-blue-700">
                    <User class="size-4" />
                  </div>
                  <div class="min-w-0">
                    <h2 class="text-base sm:text-lg font-bold text-[#0c234a] truncate">
                      ১. আপনার ব্যক্তিগত তথ্য <span class="hidden sm:inline text-xs font-normal text-slate-500">· personal details</span>
                    </h2>
                    <p class="text-[11px] sm:text-xs text-slate-500 truncate">আপনার এন্ট্রি পাস ও ব্যাজে যেভাবে তথ্য থাকবে।</p>
                  </div>
                </div>
                <span class="rounded-full bg-blue-50 px-2.5 py-0.5 text-[10px] sm:text-xs font-medium text-blue-700 shrink-0">
                  ধাপ ১ / ৩
                </span>
              </div>

              <div class="grid gap-3.5 sm:gap-4 sm:grid-cols-2">
                <!-- Full Name -->
                <div class="sm:col-span-2">
                  <label class="mb-1 block text-xs font-semibold text-slate-700">
                    Full Name <span class="text-rose-500">*</span>
                  </label>
                  <div class="relative">
                    <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-blue-600">
                      <User class="size-4" />
                    </div>
                    <input
                      v-model.trim="form.fullName"
                      type="text"
                      required
                      class="block w-full min-h-[46px] rounded-xl border border-slate-200 bg-slate-50/60 py-2.5 pl-10 pr-4 text-base sm:text-sm text-[#0f2747] placeholder-slate-400 transition focus:border-blue-600 focus:bg-white focus:outline-none"
                    />
                  </div>
                </div>

                <!-- Quanta ID -->
                <div>
                  <label class="mb-1 block text-xs font-semibold text-slate-700">
                    Quanta ID <span class="text-rose-500">*</span>
                  </label>
                  <div class="relative">
                    <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-indigo-600">
                      <IdCard class="size-4" />
                    </div>
                    <input
                      v-model.trim="form.quantaId"
                      type="text"
                      required
                      class="block w-full min-h-[46px] rounded-xl border border-slate-200 bg-slate-50/60 py-2.5 pl-10 pr-4 text-base sm:text-sm text-[#0f2747] placeholder-slate-400 transition focus:border-blue-600 focus:bg-white focus:outline-none"
                    />
                  </div>
                </div>

                <!-- Batch / Passing Year -->
                <div>
                  <label class="mb-1 block text-xs font-semibold text-slate-700">
                    ব্যাচ বা পাসের বছর (batch / year)
                  </label>
                  <div class="relative">
                    <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-amber-600">
                      <GraduationCap class="size-4" />
                    </div>
                    <input
                      v-model.trim="form.batch"
                      type="text"
                      placeholder="যেমন: ssc 2018 / hsc 2020"
                      class="block w-full min-h-[46px] rounded-xl border border-slate-200 bg-slate-50/60 py-2.5 pl-10 pr-4 text-base sm:text-sm text-[#0f2747] placeholder-slate-400 transition focus:border-blue-600 focus:bg-white focus:outline-none"
                    />
                  </div>
                </div>

                <!-- Mobile Number -->
                <div>
                  <div class="mb-1 flex items-center justify-between">
                    <label class="block text-xs font-semibold text-slate-700">
                      Mobile Number <span class="text-rose-500">*</span>
                    </label>
                    <span v-if="isMobileValid" class="flex items-center gap-1 text-[11px] font-semibold text-emerald-600">
                      <CheckCircle2 class="size-3.5" /> Valid
                    </span>
                  </div>
                  <div class="relative">
                    <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-emerald-600">
                      <Phone class="size-4" />
                    </div>
                    <input
                      v-model.trim="form.mobileNumber"
                      type="tel"
                      inputmode="tel"
                      required
                      pattern="01[3-9][0-9]{8}"
                      title="১১ ডিজিটের বাংলাদেশি মোবাইল নম্বর দিন, যেমন: 01XXXXXXXXX"
                      placeholder="01XXXXXXXXX"
                      class="block w-full min-h-[46px] rounded-xl border border-slate-200 bg-slate-50/60 py-2.5 pl-10 pr-4 text-base sm:text-sm text-[#0f2747] placeholder-slate-400 transition focus:border-blue-600 focus:bg-white focus:outline-none"
                    />
                  </div>
                </div>

                <!-- Gender Selector with PROPER RADIO BUTTONS -->
                <div>
                  <label class="mb-1 block text-xs font-semibold text-slate-700">
                    লিঙ্গ (gender) <span class="text-rose-500">*</span>
                  </label>
                  <div class="grid grid-cols-2 gap-2">
                    <!-- Male Radio Button -->
                    <label
                      class="relative flex min-h-[46px] cursor-pointer items-center justify-center sm:justify-start gap-2.5 rounded-xl border p-2.5 transition select-none"
                      :class="form.gender === 'male' ? 'border-blue-600 bg-blue-50/80' : 'border-slate-200 bg-white hover:border-slate-300'"
                    >
                      <input
                        type="radio"
                        name="main_gender"
                        value="male"
                        v-model="form.gender"
                        class="sr-only"
                      />
                      <div
                        class="flex size-4.5 shrink-0 items-center justify-center rounded-full border-2 transition"
                        :class="form.gender === 'male' ? 'border-blue-600 bg-blue-600' : 'border-slate-300 bg-white'"
                      >
                        <div v-if="form.gender === 'male'" class="size-1.5 rounded-full bg-white"></div>
                      </div>
                      <span class="text-xs font-semibold text-[#0f2747]">
                        Male
                      </span>
                    </label>

                    <!-- Female Radio Button -->
                    <label
                      class="relative flex min-h-[46px] cursor-pointer items-center justify-center sm:justify-start gap-2.5 rounded-xl border p-2.5 transition select-none"
                      :class="form.gender === 'female' ? 'border-blue-600 bg-blue-50/80' : 'border-slate-200 bg-white hover:border-slate-300'"
                    >
                      <input
                        type="radio"
                        name="main_gender"
                        value="female"
                        v-model="form.gender"
                        class="sr-only"
                      />
                      <div
                        class="flex size-4.5 shrink-0 items-center justify-center rounded-full border-2 transition"
                        :class="form.gender === 'female' ? 'border-blue-600 bg-blue-600' : 'border-slate-300 bg-white'"
                      >
                        <div v-if="form.gender === 'female'" class="size-1.5 rounded-full bg-white"></div>
                      </div>
                      <span class="text-xs font-semibold text-[#0f2747]">
                        Female
                      </span>
                    </label>
                  </div>
                </div>

                <!-- T-Shirt Size SELECT OPTION DROPDOWN LIST -->
                <div class="sm:col-span-2">
                  <div class="mb-1 flex items-center justify-between">
                    <label class="block text-xs font-semibold text-slate-700">
                      T-Shirt Size <span class="text-rose-500">*</span>
                    </label>
                    <span class="text-[11px] text-blue-700 font-medium">উপহার হিসেবে দেয়া হবে</span>
                  </div>
                  <div class="relative">
                    <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-blue-600">
                      <Shirt class="size-4" />
                    </div>
                    <select
                      v-model="form.tShirtSize"
                      required
                      class="block w-full min-h-[46px] appearance-none rounded-xl border border-slate-200 bg-slate-50/60 py-2.5 pl-10 pr-10 text-base sm:text-sm text-[#0f2747] transition focus:border-blue-600 focus:bg-white focus:outline-none"
                    >
                      <option value="" disabled>Select T-Shirt Size</option>
                      <option v-for="s in tShirtSizes" :key="s.code" :value="s.code">
                        {{ s.label }} — চেস্ট সাইজ: {{ s.chest }}
                      </option>
                    </select>
                    <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center pr-3.5 text-slate-400">
                      <ChevronDown class="size-4" />
                    </div>
                  </div>
                </div>

                <!-- Occupation -->
                <div>
                  <label class="mb-1 block text-xs font-semibold text-slate-700">
                    Occupation <span class="text-rose-500">*</span>
                  </label>
                  <div class="relative">
                    <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-sky-600">
                      <Briefcase class="size-4" />
                    </div>
                    <input
                      v-model.trim="form.occupation"
                      type="text"
                      required
                      placeholder="e.g., Student / Teacher / Employee"
                      class="block w-full min-h-[46px] rounded-xl border border-slate-200 bg-slate-50/60 py-2.5 pl-10 pr-4 text-base sm:text-sm text-[#0f2747] placeholder-slate-400 transition focus:border-blue-600 focus:bg-white focus:outline-none"
                    />
                  </div>
                </div>

                <!-- Institution -->
                <div>
                  <label class="mb-1 block text-xs font-semibold text-slate-700">
                    Institution / Workplace <span class="text-rose-500">*</span>
                  </label>
                  <div class="relative">
                    <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-violet-600">
                      <Building2 class="size-4" />
                    </div>
                    <input
                      v-model.trim="form.institution"
                      type="text"
                      required
                      placeholder="e.g., University, College or Workplace"
                      class="block w-full min-h-[46px] rounded-xl border border-slate-200 bg-slate-50/60 py-2.5 pl-10 pr-4 text-base sm:text-sm text-[#0f2747] placeholder-slate-400 transition focus:border-blue-600 focus:bg-white focus:outline-none"
                    />
                  </div>
                </div>
              </div>
            </section>


            <!-- STEP 2: GUESTS & FAMILY -->
            <section class="rounded-2xl bg-white p-4 sm:p-6 lg:p-7">
              <div class="mb-4 sm:mb-5 flex flex-wrap items-center justify-between gap-2 pb-2.5 border-b border-slate-100">
                <div class="flex items-center gap-2.5">
                  <div class="flex size-8 sm:size-9 shrink-0 items-center justify-center rounded-lg bg-indigo-50 text-indigo-700">
                    <Users class="size-4" />
                  </div>
                  <div>
                    <h2 class="text-base sm:text-lg font-bold text-[#0c234a]">
                      2. Guests & Family Information <span class="text-xs font-normal text-slate-500">· guests</span>
                    </h2>
                    <p class="text-[11px] sm:text-xs text-slate-500">
                      No additional fee for guests.
                    </p>
                  </div>
                </div>

                <span class="rounded-full bg-emerald-50 px-2.5 py-0.5 text-xs font-semibold text-emerald-700">
                  Free (No Charge)
                </span>
              </div>

              <!-- Clean Empty State -->
              <div v-if="form.guests.length === 0" class="rounded-xl bg-slate-50/70 p-4 sm:p-6 text-center">
                <div class="mx-auto flex size-11 sm:size-12 items-center justify-center rounded-full bg-blue-100 text-blue-700">
                  <UserPlus class="size-5 sm:size-6" />
                </div>
                
                <h3 class="mt-2.5 text-xs sm:text-sm font-bold text-[#0c234a]">
                  প্রিয়জনদের সাথে উৎসবে যোগ দিন
                </h3>
                <p class="mx-auto mt-1 max-w-md text-[11px] sm:text-xs text-slate-600">
                  স্পাউস, সন্তান বা পরিবারের সদস্যদের নিয়ে আসুন কোয়ান্টামমে। প্রত্যেক অতিথির জন্য আলাদা উৎসব টি-শার্ট ও বিশেষ ভোজের সুব্যবস্থা থাকবে।
                </p>

                <div class="mt-3.5">
                  <button
                    type="button"
                    class="inline-flex min-h-[44px] items-center gap-1.5 rounded-xl bg-blue-700 px-4 py-2.5 text-xs font-semibold text-white transition hover:bg-blue-800"
                    @click="addGuest"
                  >
                    <Plus class="size-4" />
                    <span>Add Guest</span>
                  </button>
                </div>
              </div>

              <!-- Clean Guest Cards List -->
              <div v-else class="space-y-3.5 sm:space-y-4">
                <div
                  v-for="(guest, index) in form.guests"
                  :key="guest.id"
                  class="rounded-xl bg-slate-50/70 p-3.5 sm:p-5"
                >
                  <div class="mb-3 flex items-center justify-between pb-2 border-b border-slate-200/50">
                    <div class="flex items-center gap-2">
                      <span class="flex size-5 sm:size-6 items-center justify-center rounded-md bg-blue-700 text-[10px] sm:text-xs font-bold text-white">
                        {{ index + 1 }}
                      </span>
                      <h3 class="text-xs font-bold text-[#0c234a]">
                        অতিথি #{{ index + 1 }}
                      </h3>
                    </div>

                    <button
                      type="button"
                      class="flex min-h-[36px] items-center gap-1 px-2 text-xs font-medium text-rose-600 hover:text-rose-800"
                      @click="removeGuest(guest.id)"
                    >
                      <Trash2 class="size-3.5" />
                      <span>মুছে ফেলুন</span>
                    </button>
                  </div>

                  <div class="grid gap-3 sm:gap-4 sm:grid-cols-2">
                    <!-- Guest Name -->
                    <div>
                      <label class="mb-1 block text-xs font-semibold text-slate-700">
                        অতিথির পুরো নাম <span class="text-rose-500">*</span>
                      </label>
                      <div class="relative">
                        <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-indigo-600">
                          <User class="size-4" />
                        </div>
                        <input
                          v-model.trim="guest.name"
                          type="text"
                          required
                          placeholder="অতিথির পুরো নাম লিখুন"
                          class="block w-full min-h-[44px] rounded-xl border border-slate-200 bg-white py-2 pl-10 pr-3 text-base sm:text-xs text-[#0f2747] placeholder-slate-400 focus:border-blue-600 focus:outline-none"
                        />
                      </div>
                    </div>

                    <!-- Guest Mobile -->
                    <div>
                      <label class="mb-1 block text-xs font-semibold text-slate-700">
                        মোবাইল নম্বর
                      </label>
                      <div class="relative">
                        <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-emerald-600">
                          <Phone class="size-4" />
                        </div>
                        <input
                          v-model.trim="guest.number"
                          type="tel"
                          inputmode="tel"
                          placeholder="01XXXXXXXXX"
                          class="block w-full min-h-[44px] rounded-xl border border-slate-200 bg-white py-2 pl-10 pr-3 text-base sm:text-xs text-[#0f2747] placeholder-slate-400 focus:border-blue-600 focus:outline-none"
                        />
                      </div>
                    </div>

                    <!-- Relationship Selector with Responsive Chips -->
                    <div class="sm:col-span-2">
                      <label class="mb-1 block text-xs font-semibold text-slate-700">
                        সম্পর্ক (relationship) <span class="text-rose-500">*</span>
                      </label>
                      <div class="flex flex-wrap gap-1.5">
                        <button
                          v-for="rel in relations"
                          :key="rel.code"
                          type="button"
                          class="inline-flex min-h-[36px] items-center gap-1.5 rounded-lg border px-2.5 py-1 text-xs font-medium transition"
                          :class="guest.relation === rel.code ? 'border-blue-600 bg-blue-50 text-blue-900 font-bold' : 'border-slate-200 bg-white text-slate-700 hover:border-slate-300'"
                          @click="guest.relation = rel.code"
                        >
                          <Heart v-if="rel.icon === 'heart'" class="size-3 text-rose-500 shrink-0" />
                          <Smile v-else-if="rel.icon === 'smile'" class="size-3 text-amber-500 shrink-0" />
                          <Users v-else-if="rel.icon === 'users'" class="size-3 text-blue-500 shrink-0" />
                          <UserPlus v-else-if="rel.icon === 'userplus'" class="size-3 text-emerald-500 shrink-0" />
                          <Handshake v-else-if="rel.icon === 'handshake'" class="size-3 text-sky-500 shrink-0" />
                          <User v-else class="size-3 text-slate-400 shrink-0" />
                          <span>{{ rel.label }}</span>
                        </button>
                      </div>
                    </div>

                    <!-- Guest Gender with PROPER RADIO BUTTONS -->
                    <div>
                      <label class="mb-1 block text-xs font-semibold text-slate-700">
                        লিঙ্গ (gender)
                      </label>
                      <div class="grid grid-cols-2 gap-2">
                        <label
                          class="relative flex min-h-[44px] cursor-pointer items-center justify-center sm:justify-start gap-2 rounded-xl border p-2 transition select-none"
                          :class="guest.gender === 'male' ? 'border-blue-600 bg-blue-50' : 'border-slate-200 bg-white hover:border-slate-300'"
                        >
                          <input
                            type="radio"
                            :name="'guest_gender_' + guest.id"
                            value="male"
                            v-model="guest.gender"
                            class="sr-only"
                          />
                          <div
                            class="flex size-4 shrink-0 items-center justify-center rounded-full border-2 transition"
                            :class="guest.gender === 'male' ? 'border-blue-600 bg-blue-600' : 'border-slate-300 bg-white'"
                          >
                            <div v-if="guest.gender === 'male'" class="size-1.5 rounded-full bg-white"></div>
                          </div>
                          <span class="text-xs font-semibold text-[#0f2747]">পুরুষ</span>
                        </label>

                        <label
                          class="relative flex min-h-[44px] cursor-pointer items-center justify-center sm:justify-start gap-2 rounded-xl border p-2 transition select-none"
                          :class="guest.gender === 'female' ? 'border-blue-600 bg-blue-50' : 'border-slate-200 bg-white hover:border-slate-300'"
                        >
                          <input
                            type="radio"
                            :name="'guest_gender_' + guest.id"
                            value="female"
                            v-model="guest.gender"
                            class="sr-only"
                          />
                          <div
                            class="flex size-4 shrink-0 items-center justify-center rounded-full border-2 transition"
                            :class="guest.gender === 'female' ? 'border-blue-600 bg-blue-600' : 'border-slate-300 bg-white'"
                          >
                            <div v-if="guest.gender === 'female'" class="size-1.5 rounded-full bg-white"></div>
                          </div>
                          <span class="text-xs font-semibold text-[#0f2747]">মহিলা</span>
                        </label>
                      </div>
                    </div>

                    <!-- Guest T-Shirt Size SELECT OPTION DROPDOWN LIST -->
                    <div>
                      <label class="mb-1 block text-xs font-semibold text-slate-700">
                        টি-শার্ট সাইজ (t-shirt size)
                      </label>
                      <div class="relative">
                        <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3 text-indigo-600">
                          <Shirt class="size-4" />
                        </div>
                        <select
                          v-model="guest.tShirtSize"
                          class="block w-full min-h-[44px] appearance-none rounded-xl border border-slate-200 bg-white py-2 pl-9 pr-8 text-base sm:text-xs text-[#0f2747] transition focus:border-blue-600 focus:outline-none"
                        >
                          <option v-for="s in tShirtSizes" :key="s.code" :value="s.code">
                            {{ s.label }} — চেস্ট: {{ s.chest }}
                          </option>
                        </select>
                        <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center pr-3 text-slate-400">
                          <ChevronDown class="size-3.5" />
                        </div>
                      </div>
                    </div>
                  </div>
                </div>

                <!-- Add More Guest Button -->
                <div class="pt-1">
                  <button
                    type="button"
                    class="flex w-full min-h-[44px] items-center justify-center gap-2 rounded-xl border border-blue-600 bg-blue-50/50 py-2.5 text-xs font-bold text-blue-700 transition hover:bg-blue-100"
                    @click="addGuest"
                  >
                    <Plus class="size-4" />
                    <span>আরও একজন অতিথি যোগ করুন (add guest)</span>
                  </button>
                </div>
              </div>
            </section>


            <!-- STEP 3: PAYMENT DETAILS -->
            <section class="rounded-2xl bg-white p-4 sm:p-6 lg:p-7">
              <div class="mb-4 sm:mb-5 flex items-center justify-between pb-2.5 border-b border-slate-100">
                <div class="flex items-center gap-2.5">
                  <div class="flex size-8 sm:size-9 shrink-0 items-center justify-center rounded-lg bg-emerald-50 text-emerald-700">
                    <CreditCard class="size-4" />
                  </div>
                  <div>
                    <h2 class="text-base sm:text-lg font-bold text-[#0c234a]">
                      ৩. পেমেন্টের তথ্য <span class="hidden sm:inline text-xs font-normal text-slate-500">· payment details</span>
                    </h2>
                    <p class="text-[11px] sm:text-xs text-slate-500">বিকাশ, নগদ, রকেট বা স্পট পেমেন্ট।</p>
                  </div>
                </div>
                <div class="text-right">
                  <span class="text-xs text-slate-500">মোট প্রদেয়:</span>
                  <span class="ml-1 text-base font-bold text-blue-800">৳{{ totalFee }}</span>
                </div>
              </div>

              <!-- Payment Method Selector (Responsive Grid with 5th Item Spanning Full Width on Mobile) -->
              <div>
                <label class="mb-2 block text-xs font-semibold text-slate-700">
                  Select a Payment Method <span class="text-rose-500">*</span>
                </label>
                <div class="grid grid-cols-2 gap-2 sm:grid-cols-3 lg:grid-cols-5">
                  <button
                    v-for="(pm, idx) in paymentMethods"
                    :key="pm.id"
                    type="button"
                    class="relative flex min-h-[72px] flex-col items-center justify-center rounded-xl border p-2.5 text-center transition"
                    :class="[
                      form.paymentMethod === pm.id ? pm.activeBg + ' font-bold' : 'border-slate-200 bg-white text-slate-600 hover:border-slate-300',
                      idx === 4 ? 'col-span-2 sm:col-span-1' : ''
                    ]"
                    @click="form.paymentMethod = pm.id"
                  >
                    <!-- Brand Icon Graphic -->
                    <div class="mb-1 flex size-7 sm:size-8 items-center justify-center">
                      <!-- bKash Origami Bird SVG -->
                      <svg v-if="pm.id === 'bkash'" viewBox="0 0 100 100" class="size-7" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <path d="M12 48L42 16L72 48L52 88L32 72L12 48Z" fill="#E2136E"/>
                        <path d="M42 16L82 28L72 48L42 16Z" fill="#D12053"/>
                        <path d="M72 48L92 42L78 64L72 48Z" fill="#B31A45"/>
                        <path d="M52 88L68 96L78 64L52 88Z" fill="#E2136E"/>
                      </svg>

                      <!-- Nagad Swirl/Flame SVG -->
                      <svg v-else-if="pm.id === 'nagad'" viewBox="0 0 100 100" class="size-7" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <circle cx="50" cy="50" r="44" fill="#FFF2E8"/>
                        <path d="M50 16C36 28 32 44 38 56C42 64 50 68 56 64C64 60 66 48 58 40C52 34 50 26 50 16Z" fill="#F7941D"/>
                        <path d="M54 36C48 42 46 52 50 58C54 62 60 62 64 58C68 54 68 46 62 42C58 38 56 34 54 36Z" fill="#ED1C24"/>
                      </svg>

                      <!-- Rocket DBBL Flyer SVG -->
                      <svg v-else-if="pm.id === 'rocket'" viewBox="0 0 100 100" class="size-7" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <circle cx="50" cy="50" r="44" fill="#F6EBF8"/>
                        <path d="M50 14L66 44H34L50 14Z" fill="#8C3494"/>
                        <path d="M34 44H66V68C66 74 60 80 50 82C40 80 34 74 34 68V44Z" fill="#75227C"/>
                        <circle cx="50" cy="54" r="7" fill="#FFFFFF"/>
                        <path d="M42 82L50 94L58 82H42Z" fill="#ED1C24"/>
                      </svg>

                      <!-- Bank Transfer Building SVG -->
                      <svg v-else-if="pm.id === 'bank'" viewBox="0 0 100 100" class="size-7" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <circle cx="50" cy="50" r="44" fill="#EFF6FF"/>
                        <path d="M50 18L18 34V40H82V34L50 18Z" fill="#1D4ED8"/>
                        <rect x="24" y="44" width="8" height="28" rx="2" fill="#2563EB"/>
                        <rect x="38" y="44" width="8" height="28" rx="2" fill="#2563EB"/>
                        <rect x="54" y="44" width="8" height="28" rx="2" fill="#2563EB"/>
                        <rect x="68" y="44" width="8" height="28" rx="2" fill="#2563EB"/>
                        <rect x="16" y="74" width="68" height="8" rx="2" fill="#1D4ED8"/>
                      </svg>

                      <!-- Cash / Spot Banknote SVG -->
                      <svg v-else viewBox="0 0 100 100" class="size-7" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <circle cx="50" cy="50" r="44" fill="#ECFDF5"/>
                        <rect x="16" y="26" width="68" height="42" rx="6" fill="#059669"/>
                        <rect x="22" y="32" width="56" height="30" rx="4" stroke="#A7F3D0" stroke-width="2"/>
                        <circle cx="50" cy="47" r="8" fill="#A7F3D0"/>
                        <path d="M30 68L70 68L80 76L20 76L30 68Z" fill="#047857"/>
                      </svg>
                    </div>

                    <span class="text-xs font-bold leading-tight">{{ pm.name }}</span>
                    <span class="text-[10px] text-slate-500">{{ pm.bnName }}</span>

                    <!-- Checked indicator -->
                    <span
                      v-if="form.paymentMethod === pm.id"
                      class="absolute -top-1 -right-1 flex size-4 items-center justify-center rounded-full bg-blue-700 text-[10px] text-white"
                    >
                      ✓
                    </span>
                  </button>
                </div>
              </div>

              <!-- Dynamic Instruction Box (Mobile Friendly Wrap) -->
              <div class="mt-4 rounded-xl bg-blue-50/60 p-3.5 sm:p-4">
                <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-2.5">
                  <div>
                    <div class="mt-0.5 flex flex-wrap items-center gap-2">
                      <span class="font-mono text-base sm:text-lg font-bold text-blue-950">
                        {{ selectedPaymentConfig.number }}
                      </span>
                      <span class="rounded bg-blue-100 px-2 py-0.5 text-[10px] sm:text-[11px] text-blue-800 font-medium">
                        {{ selectedPaymentConfig.type }}
                      </span>
                    </div>
                  </div>

                  <button
                    v-if="form.paymentMethod !== 'cash'"
                    type="button"
                    class="inline-flex min-h-[40px] items-center justify-center gap-1.5 rounded-lg bg-white px-3.5 py-2 text-xs font-semibold text-blue-800 transition hover:bg-blue-100 active:scale-95 border border-slate-200 w-full sm:w-auto"
                    @click="copyPaymentNumber(selectedPaymentConfig.number)"
                  >
                    <Check v-if="copied" class="size-3.5 text-emerald-600" />
                    <Copy v-else class="size-3.5" />
                    <span>{{ copied ? 'কপি হয়েছে! ✓' : 'নম্বর কপি করুন' }}</span>
                  </button>
                </div>

                <div class="mt-2.5 flex items-start gap-2 border-t border-slate-200/60 pt-2 text-[11px] sm:text-xs text-slate-600">
                  <Info class="size-4 shrink-0 text-blue-700 mt-0.5" />
                  <p>
                    <strong class="text-blue-950">নির্দেশনা:</strong> {{ selectedPaymentConfig.instructions }} মোট প্রদেয় পরিমাণ <strong>৳{{ totalFee }}</strong> টাকা। ফি পাঠানোর পর নিচের ঘরে প্রেরক নম্বর ও ট্রানজেকশন আইডি দিন।
                  </p>
                </div>
              </div>

              <!-- Sender Info & TrxID -->
              <div class="mt-4 grid gap-3.5 sm:gap-4 sm:grid-cols-2">
                <!-- Sender Number -->
                <div>
                  <div class="mb-1 flex items-center justify-between">
                    <label class="block text-xs font-semibold text-slate-700">
                      প্রেরক নম্বর (sender number) <span class="text-rose-500">*</span>
                    </label>
                    <button
                      type="button"
                      class="text-[11px] text-blue-700 hover:underline font-medium"
                      @click="toggleSameAsMyNumber"
                    >
                      আমার নম্বরের মতো
                    </button>
                  </div>
                  <div class="relative">
                    <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-emerald-600">
                      <Phone class="size-4" />
                    </div>
                    <input
                      v-model.trim="form.senderNumber"
                      type="tel"
                      inputmode="tel"
                      required
                      placeholder="01XXXXXXXXX"
                      class="block w-full min-h-[46px] rounded-xl border border-slate-200 bg-slate-50/60 py-2.5 pl-10 pr-4 text-base sm:text-sm text-[#0f2747] placeholder-slate-400 transition focus:border-blue-600 focus:bg-white focus:outline-none"
                    />
                  </div>
                </div>

                <!-- Transaction ID -->
                <div>
                  <label class="mb-1 block text-xs font-semibold text-slate-700">
                    ট্রানজেকশন আইডি (trxid) <span class="text-rose-500">*</span>
                  </label>
                  <div class="relative">
                    <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3.5 text-indigo-600">
                      <CreditCard class="size-4" />
                    </div>
                    <input
                      v-model.trim="form.transactionId"
                      type="text"
                      required
                      placeholder="যেমন: bkt9832a1z (ক্যাশ হলে 'cash')"
                      class="block w-full min-h-[46px] rounded-xl border border-slate-200 bg-slate-50/60 py-2.5 pl-10 pr-4 text-base sm:text-sm font-mono text-[#0f2747] placeholder-slate-400 transition focus:border-blue-600 focus:bg-white focus:outline-none"
                    />
                  </div>
                  <p class="mt-1 text-[10px] sm:text-[11px] text-slate-500">
                    পেমেন্ট কনফার্মেশন মেসেজ থেকে প্রাপ্ত কোডটি লিখুন।
                  </p>
                </div>
              </div>
            </section>
          </div>

          <!-- SIDEBAR: LIVE PASS PREVIEW & SUMMARY (DESKTOP & IN-FLOW ON MOBILE) -->
          <aside class="space-y-4 lg:sticky lg:top-6">
            <div class="rounded-2xl bg-white p-4 sm:p-5">
              <div class="flex items-center justify-between pb-2.5 border-b border-slate-100">
                <span class="inline-flex items-center gap-1.5 text-xs font-bold text-blue-900">
                  <Ticket class="size-4 text-blue-700" /> এন্ট্রি পাস প্রিভিউ
                </span>
                <span class="rounded-full bg-blue-50 px-2 py-0.5 text-[10px] font-semibold text-blue-700">
                  লাইভ প্রিভিউ
                </span>
              </div>

              <!-- Mini Pass Card -->
              <div class="mt-3 rounded-xl bg-blue-50/60 p-3.5 sm:p-4 text-center">
                <span class="inline-block rounded-full bg-blue-700 px-2.5 py-0.5 text-[10px] font-bold text-white">
                  ঘরে ফেরা আনন্দ উৎসব ২০২৭
                </span>
                <h3 class="mt-1.5 text-sm sm:text-base font-bold text-blue-950 truncate">
                  {{ form.fullName || 'আপনার নাম (your name)' }}
                </h3>
                <p class="font-mono text-xs text-blue-700">
                  আইডি: {{ form.quantaId || 'qcsc-xxxx' }}
                </p>

                <div class="mt-2 flex justify-center gap-1.5 text-xs">
                  <span class="rounded bg-white px-2 py-0.5 text-slate-600">সাইজ: {{ form.tShirtSize }}</span>
                  <span class="rounded bg-white px-2 py-0.5 text-slate-600">{{ form.gender }}</span>
                </div>
              </div>

              <!-- Price Breakdown -->
              <div class="mt-3.5 space-y-2 border-b border-slate-100 pb-3 text-xs">
                <div class="flex items-center justify-between text-slate-600">
                  <span>নিবন্ধন ফি (registration fee)</span>
                  <span class="font-semibold text-slate-900">৳{{ registrationFee }}</span>
                </div>

                <div v-if="form.guests.length" class="flex items-center justify-between text-slate-600">
                  <span>অতিথিবৃন্দ ({{ form.guests.length }} জন)</span>
                  <span class="font-semibold text-emerald-600">ফ্রি (বিনামূল্যে)</span>
                </div>

                <div class="flex items-center justify-between text-slate-500 text-[11px]">
                  <span>মোট সদস্য সংখ্যা</span>
                  <span>{{ attendeeCount }} জন</span>
                </div>
              </div>

              <!-- Total Payable -->
              <div class="mt-3 flex items-baseline justify-between">
                <div>
                  <span class="text-xs text-slate-500">সর্বমোট প্রদেয় ফি</span>
                  <p class="text-[11px] text-blue-800 font-medium">total registration fee</p>
                </div>
                <div class="text-right">
                  <span class="text-2xl font-black text-blue-950">৳{{ totalFee }}</span>
                </div>
              </div>

              <!-- Submit Button -->
              <button
                type="submit"
                class="mt-4 flex w-full min-h-[48px] items-center justify-center gap-2 rounded-xl bg-blue-700 px-5 py-3 text-center text-sm font-bold text-white transition hover:bg-blue-800 active:scale-[0.99]"
              >
                <span>নিবন্ধন সম্পন্ন করুন</span>
                <ArrowRight class="size-4" />
              </button>

              <p class="mt-2 text-center text-[10px] sm:text-[11px] text-slate-500">
                তথ্য যাচাই করে এন্ট্রি পাস সংগ্রহ করুন।
              </p>
            </div>

            <!-- Help / Hotline Card -->
            <div class="rounded-xl bg-white p-3.5 sm:p-4 text-xs">
              <div class="flex items-start gap-2.5">
                <div class="flex size-7 shrink-0 items-center justify-center rounded-lg bg-blue-50 text-blue-700">
                  <HelpCircle class="size-4" />
                </div>
                <div>
                  <p class="font-bold text-[#0c234a]">সহায়তা হেল্পলাইন (helpline)</p>
                  <p class="mt-0.5 text-slate-500">
                    নিবন্ধন বা যাতায়াত সংক্রান্ত কোনো প্রশ্নে:
                  </p>
                  <p class="mt-1 font-mono font-semibold text-blue-800">
                    📞 01700-000000 / 01800-000000
                  </p>
                </div>
              </div>
            </div>
          </aside>
        </form>

        <!-- STICKY MOBILE BOTTOM BAR (FOR SCREENS < LG) -->
        <div class="lg:hidden fixed bottom-0 left-0 right-0 z-40 bg-white border-t border-slate-200 px-4 py-2.5 flex items-center justify-between gap-3 shadow-lg">
          <div>
            <span class="text-[10px] text-slate-500 block leading-tight">সর্বমোট ফি</span>
            <span class="text-lg font-black text-blue-900 leading-none">৳{{ totalFee }}</span>
          </div>

          <button
            type="button"
            class="flex items-center gap-1.5 rounded-xl bg-blue-700 px-4 py-2.5 text-xs font-bold text-white transition hover:bg-blue-800 active:scale-95"
            @click="completeRegistration"
          >
            <span>নিবন্ধন সম্পন্ন করুন</span>
            <ArrowRight class="size-3.5" />
          </button>
        </div>

        <!-- Footer -->
        <footer class="mt-10 sm:mt-12 border-t border-slate-200/80 pt-5 sm:pt-6 text-center text-xs text-slate-500">
          <div class="flex flex-col items-center justify-between gap-2.5 sm:flex-row">
            <div class="flex items-center gap-1.5 font-medium text-blue-900">
              <GraduationCap class="size-4 text-blue-700 shrink-0" />
              <span class="truncate">কোয়ান্টাম কসমো স্কুল ও কলেজ • কোয়ান্টামম, লামা, বান্দরবান</span>
            </div>
            <div>
              <span>ঘরে ফেরা আনন্দ উৎসব ২০২৭ • ১৬-১৭ ডিসেম্বর ২০২৬</span>
            </div>
          </div>
        </footer>
      </template>
    </div>
  </div>
</template>
