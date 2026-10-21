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
  UserPlus,
  Download,
  Send,
  Receipt,
  Lock,
  X,
  Sparkles
} from '@lucide/vue'
import quantumLogo from './quantum-logo.png'

const registrationFee = 100
const isComplete = ref(false)
const isSubmitting = ref(false)
const copied = ref(false)
const sampleDataFilled = ref(false)
const registrationRefNumber = ref('')

const tShirtSizes = [
  { code: 'M', label: 'M (27")', length: '27"' },
  { code: 'L', label: 'L (28")', length: '28"' },
  { code: 'XL', label: 'XL (29")', length: '29"' },
  { code: 'XXL', label: 'XXL (30")', length: '30"' },
  { code: '3XL', label: '3XL (31")', length: '31"' },
]

const gatewayChannels = [
  { id: 'bkash', name: 'bKash', bnName: 'বিকাশ', color: '#E2136E' },
  { id: 'nagad', name: 'Nagad', bnName: 'নগদ', color: '#F7941D' },
  { id: 'rocket', name: 'Rocket', bnName: 'রকেট', color: '#8C3494' },
  { id: 'card', name: 'Cards', bnName: 'কার্ড', color: '#1A1F71' },
]

const showPaymentModal = ref(false)
const gatewayChannel = ref('bkash')
const gatewayStep = ref('input') // 'input' | 'processing' | 'success'
const gatewayAccount = ref('')
const gatewayPin = ref('')
const gatewayAgree = ref(true)

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

function fillSampleData() {
  form.fullName = 'আব্দুল্লাহ আল মামুন'
  form.quantaId = '10582'
  form.gender = 'male'
  form.mobileNumber = '01712345678'
  form.occupation = 'সফটওয়্যার প্রকৌশলী'
  form.institution = 'ব্র্যাক আইটি সার্ভিসেস'
  form.batch = '2016'
  form.tShirtSize = 'XL'
  form.hasSpouse = true
  form.childrenCount = 1
  form.transportOption = 'dhaka'

  sampleDataFilled.value = true
  setTimeout(() => {
    sampleDataFilled.value = false
  }, 4000)
}

function openPaymentGateway() {
  gatewayAccount.value = form.mobileNumber || ''
  gatewayPin.value = '1234'
  gatewayStep.value = 'input'
  showPaymentModal.value = true
}

function closePaymentGateway() {
  if (gatewayStep.value === 'processing') return
  showPaymentModal.value = false
}

function submitGatewayPayment() {
  gatewayStep.value = 'processing'
  setTimeout(() => {
    const randStr = Math.random().toString(36).substring(2, 8).toUpperCase()
    let prefix = 'TX'
    let channelLabel = 'Card'
    if (gatewayChannel.value === 'bkash') {
      prefix = 'BK'
      channelLabel = 'bKash'
    } else if (gatewayChannel.value === 'nagad') {
      prefix = 'NG'
      channelLabel = 'Nagad'
    } else if (gatewayChannel.value === 'rocket') {
      prefix = 'RK'
      channelLabel = 'Rocket'
    } else {
      prefix = 'CD'
      channelLabel = 'Visa/Mastercard'
    }

    form.transactionId = `${prefix}${randStr}`
    form.paymentMethod = channelLabel
    gatewayStep.value = 'success'

    setTimeout(() => {
      showPaymentModal.value = false
      completeRegistration()
    }, 700)
  }, 1300)
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

function downloadMoneyReceipt() {
  try {
    const canvas = document.createElement('canvas')
    canvas.width = 900
    canvas.height = 1250
    const ctx = canvas.getContext('2d')
    if (!ctx) return

    // Background - crisp white
    ctx.fillStyle = '#ffffff'
    ctx.fillRect(0, 0, 900, 1250)

    // Outer decorative border
    ctx.strokeStyle = '#0c234a'
    ctx.lineWidth = 4
    ctx.strokeRect(25, 25, 850, 1200)

    ctx.strokeStyle = '#d97706'
    ctx.lineWidth = 1.5
    ctx.strokeRect(32, 32, 836, 1186)

    // Header Background Band
    const headerGrad = ctx.createLinearGradient(35, 35, 865, 175)
    headerGrad.addColorStop(0, '#061836')
    headerGrad.addColorStop(0.5, '#0a234c')
    headerGrad.addColorStop(1, '#0c2e64')
    ctx.fillStyle = headerGrad
    ctx.fillRect(35, 35, 830, 155)

    // Header Title
    ctx.fillStyle = '#ffffff'
    ctx.font = 'bold 28px "SolaimanLipi", "Plus Jakarta Sans", sans-serif'
    ctx.textAlign = 'center'
    ctx.fillText('কোয়ান্টাম কসমো স্কুল ও কলেজ', 450, 80)

    ctx.font = '14px "Plus Jakarta Sans", sans-serif'
    ctx.fillStyle = '#93c5fd'
    ctx.fillText('QUANTUM COSMO SCHOOL & COLLEGE • LAMA, BANDARBAN', 450, 108)

    ctx.font = 'bold 18px "SolaimanLipi", sans-serif'
    ctx.fillStyle = '#fde68a'
    ctx.fillText('ঘরে ফেরা আনন্দ উৎসব ২০২৭ (কোয়ান্টা রিইউনিয়ন)', 450, 140)

    ctx.font = '12px "SolaimanLipi", sans-serif'
    ctx.fillStyle = '#e2e8f0'
    ctx.fillText('তারিখ: ১৬-১৭ ডিসেম্বর ২০২৬ • স্থান: কোয়ান্টামম, লামা, বান্দরবান', 450, 168)

    // Document Title Banner
    ctx.fillStyle = '#f1f5f9'
    ctx.fillRect(35, 190, 830, 48)
    ctx.strokeStyle = '#cbd5e1'
    ctx.lineWidth = 1
    ctx.strokeRect(35, 190, 830, 48)

    ctx.fillStyle = '#0c234a'
    ctx.font = 'bold 20px "SolaimanLipi", "Plus Jakarta Sans", sans-serif'
    ctx.textAlign = 'center'
    ctx.fillText('অফিসিয়াল মানি রিসিট • OFFICIAL MONEY RECEIPT', 450, 222)

    // Receipt Meta Bar (Receipt No, Date, Ref No)
    ctx.fillStyle = '#f8fafc'
    ctx.fillRect(55, 255, 790, 70)
    ctx.strokeStyle = '#e2e8f0'
    ctx.strokeRect(55, 255, 790, 70)

    const receiptNo = `MR-2027-${registrationRefNumber.value ? registrationRefNumber.value.replace(/[^0-9]/g, '') : '91823'}`
    const currentDate = new Date().toLocaleDateString('bn-BD', { day: 'numeric', month: 'long', year: 'numeric' })

    ctx.textAlign = 'left'
    ctx.fillStyle = '#64748b'
    ctx.font = '13px "SolaimanLipi", sans-serif'
    ctx.fillText('রিসিট নম্বর (Receipt No):', 75, 282)
    ctx.fillStyle = '#0c234a'
    ctx.font = 'bold 16px monospace'
    ctx.fillText(receiptNo, 75, 308)

    ctx.fillStyle = '#64748b'
    ctx.font = '13px "SolaimanLipi", sans-serif'
    ctx.fillText('তারিখ (Date):', 360, 282)
    ctx.fillStyle = '#0f172a'
    ctx.font = 'bold 15px "SolaimanLipi", sans-serif'
    ctx.fillText(currentDate, 360, 308)

    ctx.fillStyle = '#64748b'
    ctx.font = '13px "SolaimanLipi", sans-serif'
    ctx.fillText('রেফারেন্স (Ref No):', 610, 282)
    ctx.fillStyle = '#1d4ed8'
    ctx.font = 'bold 15px monospace'
    ctx.fillText(registrationRefNumber.value || 'QCSC-2027-REF', 610, 308)

    // Payer Details Box
    ctx.fillStyle = '#ffffff'
    ctx.fillRect(55, 340, 790, 140)
    ctx.strokeStyle = '#e2e8f0'
    ctx.strokeRect(55, 340, 790, 140)

    ctx.fillStyle = '#0c234a'
    ctx.font = 'bold 15px "SolaimanLipi", sans-serif'
    ctx.fillText('অর্থ প্রদানকারীর বিবরণ (Payer Details):', 75, 370)

    const payerDetails = [
      ['নাম (Full Name):', form.fullName || '-'],
      ['কোয়ান্টা আইডি:', form.quantaId || '-'],
      ['মোবাইল নম্বর:', form.mobileNumber || '-'],
      ['ব্যাচ / পাসের বছর:', form.batch || '-'],
      ['টি-শার্ট সাইজ:', `${form.tShirtSize} (${form.gender === 'male' ? 'পুরুষ' : 'মহিলা'})`],
      ['মোট সদস্য সংখ্যা:', `${attendeeCount.value} জন (স্পাউস: ${form.hasSpouse ? 'হ্যাঁ' : 'না'}, সন্তান: ${form.childrenCount || 0} জন)`]
    ]

    let py = 402
    payerDetails.forEach(([lbl, val], idx) => {
      const colX = idx % 2 === 0 ? 75 : 460
      const rowY = py + Math.floor(idx / 2) * 26
      ctx.fillStyle = '#64748b'
      ctx.font = '14px "SolaimanLipi", sans-serif'
      ctx.fillText(lbl, colX, rowY)
      ctx.fillStyle = '#0f172a'
      ctx.font = 'bold 14px "SolaimanLipi", sans-serif'
      ctx.fillText(String(val), colX + 130, rowY)
    })

    // Line Items Table Header
    ctx.fillStyle = '#0a234c'
    ctx.fillRect(55, 500, 790, 40)

    ctx.fillStyle = '#ffffff'
    ctx.font = 'bold 14px "SolaimanLipi", sans-serif'
    ctx.textAlign = 'center'
    ctx.fillText('ক্র.', 85, 526)
    ctx.textAlign = 'left'
    ctx.fillText('বিবরণ (Particulars)', 130, 526)
    ctx.textAlign = 'right'
    ctx.fillText('পরিমাণ (Amount)', 815, 526)

    // Table Rows
    const items = [
      ['১', 'ঘরে ফেরা আনন্দ উৎসব ২০২৭ - আনুষ্ঠানিক নিবন্ধন ফি', `৳${registrationFee}`],
      ['২', `যাতায়াত ব্যবস্থা (${selectedTransport.value.title})`, selectedTransport.value.feeText],
      ['৩', `স্পাউস ও সন্তান (${attendeeCount.value > 1 ? attendeeCount.value - 1 + ' জন পরিবার সদস্য' : 'প্রযোজ্য নয়'})`, 'বিনামূল্যে / অন্তর্ভুক্ত']
    ]

    let itemY = 540
    items.forEach(([sl, desc, amt], i) => {
      ctx.fillStyle = i % 2 === 0 ? '#f8fafc' : '#ffffff'
      ctx.fillRect(55, itemY, 790, 42)
      ctx.strokeStyle = '#e2e8f0'
      ctx.strokeRect(55, itemY, 790, 42)

      ctx.fillStyle = '#475569'
      ctx.font = '14px "SolaimanLipi", sans-serif'
      ctx.textAlign = 'center'
      ctx.fillText(sl, 85, itemY + 27)

      ctx.textAlign = 'left'
      ctx.fillStyle = '#0f172a'
      ctx.font = '14px "SolaimanLipi", sans-serif'
      ctx.fillText(desc, 130, itemY + 27)

      ctx.textAlign = 'right'
      ctx.fillStyle = '#0c234a'
      ctx.font = 'bold 15px "SolaimanLipi", sans-serif'
      ctx.fillText(amt, 815, itemY + 27)

      itemY += 42
    })

    // Total Amount Row
    ctx.fillStyle = '#eff6ff'
    ctx.fillRect(55, itemY, 790, 52)
    ctx.strokeStyle = '#bfdbfe'
    ctx.lineWidth = 1.5
    ctx.strokeRect(55, itemY, 790, 52)

    ctx.textAlign = 'left'
    ctx.fillStyle = '#1e3a8a'
    ctx.font = 'bold 16px "SolaimanLipi", sans-serif'
    ctx.fillText('সর্বমোট পরিশোধিত অর্থ (Total Paid Amount):', 75, itemY + 33)

    ctx.textAlign = 'right'
    ctx.fillStyle = '#0c234a'
    ctx.font = 'bold 24px "SolaimanLipi", sans-serif'
    ctx.fillText(`৳${totalFee.value}`, 815, itemY + 35)

    // Payment Gateway & Transaction Details Container
    const payBoxY = itemY + 70
    ctx.fillStyle = '#f8fafc'
    ctx.fillRect(55, payBoxY, 790, 110)
    ctx.strokeStyle = '#e2e8f0'
    ctx.strokeRect(55, payBoxY, 790, 110)

    ctx.textAlign = 'left'
    ctx.fillStyle = '#0c234a'
    ctx.font = 'bold 15px "SolaimanLipi", sans-serif'
    ctx.fillText('অনলাইন পেমেন্ট গেটওয়ে বিবরণ (Gateway Payment Details):', 75, payBoxY + 30)

    ctx.fillStyle = '#64748b'
    ctx.font = '13px "SolaimanLipi", sans-serif'
    ctx.fillText('পেমেন্ট মাধ্যম (Channel):', 75, payBoxY + 60)
    ctx.fillStyle = '#0f172a'
    ctx.font = 'bold 15px "SolaimanLipi", sans-serif'
    ctx.fillText(`${form.paymentMethod} Online Gateway`, 245, payBoxY + 60)

    ctx.fillStyle = '#64748b'
    ctx.font = '13px "SolaimanLipi", sans-serif'
    ctx.fillText('ট্রানজেকশন আইডি (Trx ID):', 75, payBoxY + 90)
    ctx.fillStyle = '#047857'
    ctx.font = 'bold 16px monospace'
    ctx.fillText(form.transactionId || 'TRX-PAID-SUCCESS', 245, payBoxY + 90)

    // PAID Official Seal Stamp
    ctx.save()
    ctx.translate(690, payBoxY + 55)
    ctx.rotate(-0.08)
    ctx.strokeStyle = '#059669'
    ctx.lineWidth = 3
    ctx.strokeRect(-80, -32, 160, 64)
    ctx.fillStyle = '#059669'
    ctx.font = 'bold 22px sans-serif'
    ctx.textAlign = 'center'
    ctx.fillText('PAID ✓', 0, 2)
    ctx.font = 'bold 12px "SolaimanLipi", sans-serif'
    ctx.fillText('পরিশোধিত', 0, 22)
    ctx.restore()

    // Signatures and Verification
    const sigY = payBoxY + 180
    ctx.strokeStyle = '#94a3b8'
    ctx.lineWidth = 1

    ctx.beginPath()
    ctx.moveTo(75, sigY)
    ctx.lineTo(260, sigY)
    ctx.stroke()

    ctx.beginPath()
    ctx.moveTo(600, sigY)
    ctx.lineTo(815, sigY)
    ctx.stroke()

    ctx.textAlign = 'center'
    ctx.fillStyle = '#64748b'
    ctx.font = '13px "SolaimanLipi", sans-serif'
    ctx.fillText('অর্থ প্রদানকারীর স্বাক্ষর', 167, sigY + 22)
    ctx.fillText('অনুমোদিত হিসাব কর্মকর্তা / কোষাধ্যক্ষ', 707, sigY + 22)
    ctx.fillStyle = '#0c234a'
    ctx.font = 'bold 12px "SolaimanLipi", sans-serif'
    ctx.fillText('কোয়ান্টাম কসমো স্কুল ও কলেজ রিইউনিয়ন কমিটি', 707, sigY + 40)

    // Bottom Security Note
    ctx.fillStyle = '#94a3b8'
    ctx.font = '11px "SolaimanLipi", sans-serif'
    ctx.textAlign = 'center'
    ctx.fillText('এটি একটি স্বয়ংক্রিয় ডিজিটাল মানি রিসিট। অনুষ্ঠানের প্রবেশ ও কিট সংগ্রহের সময় এই রিসিটটি প্রদর্শন করুন।', 450, 1205)

    // Download trigger
    const link = document.createElement('a')
    link.download = `Money_Receipt_${form.quantaId || 'QCSC'}_${receiptNo}.png`
    link.href = canvas.toDataURL('image/png')
    link.click()
  } catch (err) {
    downloadTextMoneyReceipt()
  }
}

function downloadTextMoneyReceipt() {
  const receiptNo = `MR-2027-${registrationRefNumber.value ? registrationRefNumber.value.replace(/[^0-9]/g, '') : '91823'}`
  const textContent = `=======================================================
কোয়ান্টাম কসমো স্কুল ও কলেজ
QUANTUM COSMO SCHOOL & COLLEGE
ঘরে ফেরা আনন্দ উৎসব ২০২৭ (কোয়ান্টা রিইউনিয়ন)
অফিসিয়াল মানি রিসিট • OFFICIAL MONEY RECEIPT
=======================================================
রিসিট নম্বর (Receipt No): ${receiptNo}
রেফারেন্স নম্বর (Ref No): ${registrationRefNumber.value}
তারিখ: ${new Date().toLocaleDateString('bn-BD')}

অর্থ প্রদানকারী (Payer Details):
-------------------------------------------------------
নাম: ${form.fullName}
কোয়ান্টা আইডি: ${form.quantaId}
মোবাইল নম্বর: ${form.mobileNumber}
ব্যাচ / পাসের বছর: ${form.batch || '-'}
টি-শার্ট সাইজ ও জেন্ডার: ${form.tShirtSize} (${form.gender === 'male' ? 'পুরুষ' : 'মহিলা'})
মোট অংশগ্রহণকারী: ${attendeeCount.value} জন (স্পাউস: ${form.hasSpouse ? 'হ্যাঁ' : 'না'}, সন্তান: ${form.childrenCount} জন)

পেমেন্ট ও হিসাবের বিবরণ:
-------------------------------------------------------
১. উৎসব নিবন্ধন ফি: ৳${registrationFee}
২. যাতায়াত ব্যবস্থা (${selectedTransport.value.title}): ${selectedTransport.value.feeText}
৩. পরিবার সদস্য: অন্তর্ভুক্ত

সর্বমোট পরিশোধিত অর্থ: ৳${totalFee.value}
পেমেন্ট মাধ্যম: ${form.paymentMethod} Online Gateway
ট্রানজেকশন আইডি (Trx ID): ${form.transactionId}
পেমেন্ট স্ট্যাটাস: PAID / সম্পূর্ণ পরিশোধিত ✓
=======================================================
কোয়ান্টাম কসমো স্কুল ও কলেজ রিইউনিয়ন কমিটি
`
  const blob = new Blob([textContent], { type: 'text/plain;charset=utf-8' })
  const url = URL.createObjectURL(blob)
  const a = document.createElement('a')
  a.href = url
  a.download = `Money_Receipt_${receiptNo}.txt`
  a.click()
  URL.revokeObjectURL(url)
}

function shareRegistrationInfo() {
  const url = window.location.href
  const receiptNo = `MR-2027-${registrationRefNumber.value ? registrationRefNumber.value.replace(/[^0-9]/g, '') : '91823'}`
  const text = `🎉 ঘরে ফেরা আনন্দ উৎসব ২০২৭ (কোয়ান্টা রিইউনিয়ন)\nআমার নিবন্ধন ও অনলাইন পেমেন্ট সম্পন্ন হয়েছে!\n\n🧾 মানি রিসিট নং: ${receiptNo}\n💳 পেমেন্ট মাধ্যম: ${form.paymentMethod}\n🔢 ট্রানজেকশন আইডি: ${form.transactionId}\n💰 পরিশোধিত ফি: ৳${totalFee.value}\n👤 নাম: ${form.fullName}\n🆔 কোয়ান্টা আইডি: ${form.quantaId}\n🚌 যাতায়াত: ${selectedTransport.value.title}\n\nআপনারাও দ্রুত নিবন্ধন করুন: ${url}`

  if (navigator.share) {
    navigator.share({
      title: 'কোয়ান্টা রিইউনিয়ন ২০২৭ পেমেন্ট ও নিবন্ধন রিসিট',
      text: text,
      url: url
    }).catch(() => {})
  } else {
    window.open(`https://api.whatsapp.com/send?text=${encodeURIComponent(text)}`, '_blank')
  }
}

function inviteAnother() {
  const url = window.location.href
  const text = `আসসালামু আলাইকুম / আদাব! প্রিয় কোয়ান্টা,\nঘরে ফেরা আনন্দ উৎসব ২০২৭ (কোয়ান্টাম কসমো স্কুল ও কলেজ রিইউনিয়ন)-এর অনলাইন রেজিস্ট্রেশন চলছে।\n\nতারিখ: ১৬-১৭ ডিসেম্বর ২০২৬\nস্থান: কোয়ান্টামম, লামা, বান্দরবান\n\nআপনিও দ্রুত এই লিংকে গিয়ে আপনার নিবন্ধন সম্পন্ন করুন:\n👉 ${url}`

  if (navigator.share) {
    navigator.share({
      title: 'ঘরে ফেরা আনন্দ উৎসব ২০২৭ - কোয়ান্টা রিইউনিয়ন এ আমন্ত্রণ',
      text: text,
      url: url
    }).catch(() => {})
  } else {
    window.open(`https://api.whatsapp.com/send?text=${encodeURIComponent(text)}`, '_blank')
  }
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
  form.paymentMethod = 'bKash'
  form.transactionId = ''

  registrationRefNumber.value = ''
  isComplete.value = false
  showPaymentModal.value = false

  try {
    localStorage.removeItem('reunion2027_registered')
  } catch (e) {
    // ignore
  }

  window.scrollTo({ top: 0, behavior: 'smooth' })
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
</script>

<template>
  <div class="min-h-screen bg-[#f1f6fd] text-[#0f2747] selection:bg-blue-600 selection:text-white pb-12">

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

          <!-- Core Payment & Confirmation Notice -->
          <div class="mt-6 rounded-2xl bg-emerald-50/90 p-4 sm:p-5 text-left border border-emerald-200">
            <div class="flex items-start gap-3.5 sm:gap-4">
              <div class="flex size-11 shrink-0 items-center justify-center rounded-xl bg-emerald-600 text-white">
                <CheckCircle2 class="size-6" />
              </div>
              <div class="min-w-0">
                <div class="flex flex-wrap items-center gap-2">
                  <h3 class="text-base sm:text-lg font-bold text-emerald-950">
                    পেমেন্ট ও নিবন্ধন সফলভাবে সম্পন্ন হয়েছে
                  </h3>
                  <span class="rounded-full bg-emerald-100 px-2.5 py-0.5 text-xs font-bold text-emerald-800">
                    PAID ✓
                  </span>
                </div>
                <p class="mt-1 text-sm sm:text-base font-normal text-emerald-900 leading-relaxed">
                  আপনার অনলাইন পেমেন্ট সফলভাবে গৃহীত হয়েছে। অনলাইন গেটওয়ে ট্রানজেকশন আইডি: <strong class="font-mono font-bold text-emerald-950">{{ form.transactionId }}</strong>।
                </p>
                <p class="mt-1.5 text-xs sm:text-sm text-emerald-700 font-medium">
                  আপনার অফিসিয়াল মানি রিসিট প্রস্তুত রয়েছে। নিচের 'মানি রিসিট ডাউনলোড' বাটনে চাপ দিয়ে রিসিটটি সংরক্ষণ করুন।
                </p>
              </div>
            </div>
          </div>

          <!-- Submission Details Summary -->
          <div class="mt-6 rounded-2xl bg-slate-50 p-4 sm:p-5 text-left border border-slate-200">
            <div class="flex items-center justify-between pb-2.5 border-b border-slate-200">
              <span class="text-xs sm:text-sm font-semibold text-slate-600">মানি রিসিট নম্বর (receipt no.)</span>
              <span class="font-mono text-sm sm:text-base font-bold text-emerald-700">
                MR-2027-{{ registrationRefNumber ? registrationRefNumber.replace(/[^0-9]/g, '') : '91823' }}
              </span>
            </div>

            <div class="flex items-center justify-between py-2.5 border-b border-slate-200">
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
                <span class="font-semibold text-slate-800">{{ form.tShirtSize }} • {{ form.gender === 'male' ? 'পুরুষ' : 'মহিলা' }}</span>
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
                <span class="font-mono font-semibold text-emerald-800">{{ form.transactionId }}</span>
              </div>
              <div class="flex items-center justify-between">
                <span class="text-slate-600 font-medium">পেমেন্ট স্ট্যাটাস:</span>
                <span class="inline-flex items-center gap-1 font-bold text-emerald-700 text-xs sm:text-sm bg-emerald-50 px-2 py-0.5 rounded border border-emerald-200">
                  <CheckCircle2 class="size-3.5" /> পরিশোধিত (PAID)
                </span>
              </div>
              <div class="flex items-center justify-between border-t border-slate-200 pt-2.5">
                <span class="font-bold text-slate-800">সর্বমোট পরিশোধিত অর্থ:</span>
                <span class="text-base font-extrabold text-blue-900">৳{{ totalFee }}</span>
              </div>
            </div>
          </div>

          <!-- Action Buttons Grid (Download Money Receipt, Share Info, Invite Another) -->
          <div class="mt-6 grid grid-cols-1 sm:grid-cols-3 gap-3">
            <!-- 1. Download Money Receipt -->
            <button
              type="button"
              class="flex items-center justify-center gap-2 rounded-xl bg-blue-700 px-4 py-3 text-xs sm:text-sm font-bold text-white transition hover:bg-blue-800 active:scale-95 min-h-[48px] shadow-sm"
              @click="downloadMoneyReceipt"
            >
              <Receipt class="size-4.5 text-blue-200 shrink-0" />
              <span>মানি রিসিট ডাউনলোড</span>
            </button>

            <!-- 2. Share Registration Info -->
            <button
              type="button"
              class="flex items-center justify-center gap-2 rounded-xl bg-emerald-600 px-4 py-3 text-xs sm:text-sm font-bold text-white transition hover:bg-emerald-700 active:scale-95 min-h-[48px] shadow-xs"
              @click="shareRegistrationInfo"
            >
              <Share2 class="size-4.5 text-emerald-200 shrink-0" />
              <span>তথ্য শেয়ার করুন</span>
            </button>

            <!-- 3. Invite Another to Register -->
            <button
              type="button"
              class="flex items-center justify-center gap-2 rounded-xl bg-indigo-600 px-4 py-3 text-xs sm:text-sm font-bold text-white transition hover:bg-indigo-700 active:scale-95 min-h-[48px] shadow-xs"
              @click="inviteAnother"
            >
              <Send class="size-4.5 text-indigo-200 shrink-0" />
              <span>আমন্ত্রণ লিংক পাঠান</span>
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
                ঘরে ফেরা আনন্দ উৎসব ২০২৬
              </h1>

              <p class="mt-2.5 text-sm sm:text-base leading-relaxed text-blue-100/90">
                পাহাড়ের কোল ঘেঁষা প্রিয় ক্যাম্পাস কোয়ান্টামমে আবারও একত্রিত হওয়ার মহোৎসব।
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

        <!-- Sample Data Quick Fill Banner -->
        <div class="mb-5 flex flex-wrap items-center justify-between gap-3 rounded-2xl bg-gradient-to-r from-amber-50 via-blue-50 to-indigo-50 p-3.5 sm:px-5 border border-amber-200/80 shadow-xs">
          <div class="flex items-center gap-3 min-w-0">
            <span class="flex size-9 shrink-0 items-center justify-center rounded-xl bg-amber-500 text-white shadow-xs">
              <Sparkles class="size-5" />
            </span>
            <div class="min-w-0">
              <p class="text-xs sm:text-sm font-bold text-[#0c234a]">
                দ্রুত টেস্ট করতে চান? (Quick Demo Test)
              </p>
              <p class="text-[11px] sm:text-xs text-slate-600 truncate sm:whitespace-normal">
                এক ক্লিকে নমুনা/ডেমো তথ্য দিয়ে পুরো ফরম পূরণ করে সরাসরি পেমেন্ট গেটওয়ে পরীক্ষা করুন।
              </p>
            </div>
          </div>

          <button
            type="button"
            class="inline-flex items-center gap-2 rounded-xl bg-amber-500 hover:bg-amber-600 active:scale-95 text-slate-950 px-4 py-2 text-xs sm:text-sm font-bold transition shadow-xs cursor-pointer shrink-0"
            @click="fillSampleData"
          >
            <Sparkles class="size-4 text-slate-950" />
            <span>নমুনা তথ্য পূরণ করুন</span>
          </button>
        </div>

        <!-- Sample Data Filled Alert -->
        <div
          v-if="sampleDataFilled"
          class="mb-5 flex items-center justify-between rounded-xl bg-emerald-50 px-4 py-3 text-xs sm:text-sm font-semibold text-emerald-900 border border-emerald-200 shadow-xs transition"
        >
          <span class="flex items-center gap-2">
            <CheckCircle2 class="size-4.5 text-emerald-600 shrink-0" />
            নমুনা তথ্য সফলভাবে পূরণ করা হয়েছে! এখন সাইডবারের "পেমেন্টে এগিয়ে যান" বাটনে ক্লিক করে গেটওয়ে পরীক্ষা করুন।
          </span>
          <button type="button" class="text-emerald-700 hover:text-emerald-900 ml-2" @click="sampleDataFilled = false">
            <X class="size-4" />
          </button>
        </div>

        <!-- Main Form Grid with Sticky Sidebar -->
        <form class="grid items-start gap-6 lg:grid-cols-[1fr_350px] xl:grid-cols-[1fr_380px]" @submit.prevent="openPaymentGateway">
          <div class="space-y-6">

            <!-- STEP 1: PERSONAL INFORMATION -->
            <section class="rounded-2xl bg-white p-5 sm:p-7">
              <div class="mb-5 flex flex-wrap items-center justify-between gap-3 pb-3 border-b border-slate-100">
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

                <!-- Step 1 Header Sample Data Quick Fill Button -->
                <button
                  type="button"
                  class="inline-flex items-center gap-1.5 rounded-lg border border-amber-300 bg-amber-50 hover:bg-amber-100 text-amber-900 px-3 py-1.5 text-xs font-bold transition shadow-2xs active:scale-95 cursor-pointer"
                  @click="fillSampleData"
                  title="দ্রুত টেস্ট করার জন্য ডেমো তথ্য পূরণ করুন"
                >
                  <Sparkles class="size-3.5 text-amber-600" />
                  <span>নমুনা তথ্য পূরণ করুন</span>
                </button>
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
          </div>

          <!-- SIDEBAR: REGISTRATION SUMMARY & PAYMENT -->
          <aside class="space-y-4 lg:sticky lg:top-6">
            <div class="rounded-2xl bg-white p-5 border border-slate-200/60 shadow-xs">
              <div class="flex items-center justify-between pb-3 border-b border-slate-100">
                <span class="inline-flex items-center gap-1.5 text-sm sm:text-base font-bold text-blue-950">
                  <CreditCard class="size-4.5 text-blue-700" />হিসাব বিবরণী (Summary)
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

              <!-- Proceed to Payment Button -->
              <button
                type="submit"
                class="mt-4 flex w-full min-h-[50px] items-center justify-center gap-2 rounded-xl bg-blue-700 px-5 py-3 text-center text-sm sm:text-base font-bold text-white transition hover:bg-blue-800 active:scale-[0.99] shadow-md"
              >
                <CreditCard class="size-4.5 text-blue-200" />
                <span>পেমেন্টে এগিয়ে যান (Pay ৳{{ totalFee }})</span>
                <ArrowRight class="size-4.5" />
              </button>

              <div class="mt-3 flex items-center justify-center gap-1.5 text-xs text-slate-500">
                <Lock class="size-3.5 text-emerald-600" />
                <span>নিরাপদ পেমেন্ট গেটওয়ে (bKash, Nagad, Cards)</span>
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

        <!-- DUMMY PAYMENT GATEWAY MODAL -->
        <Teleport to="body">
          <div
            v-if="showPaymentModal"
            class="fixed inset-0 z-50 flex items-center justify-center p-3 sm:p-4 bg-slate-900/65 backdrop-blur-xs transition-opacity"
            @click.self="closePaymentGateway"
          >
            <div class="relative w-full max-w-md rounded-2xl sm:rounded-3xl bg-white shadow-2xl overflow-hidden border border-slate-200">
              
              <!-- Gateway Header -->
              <div class="bg-gradient-to-r from-[#061836] to-[#0c234a] p-4 text-white">
                <div class="flex items-center justify-between">
                  <div class="flex items-center gap-2.5">
                    <div class="size-8 rounded-full bg-white p-1 overflow-hidden shrink-0">
                      <img :src="quantumLogo" alt="Logo" class="size-full object-contain" />
                    </div>
                    <div>
                      <h3 class="text-sm font-bold leading-tight">QCSC Payment Gateway</h3>
                      <p class="text-[11px] text-blue-200">কোয়ান্টা রিইউনিয়ন ২০২৭</p>
                    </div>
                  </div>

                  <div class="flex items-center gap-2">
                    <span class="inline-flex items-center gap-1 rounded-full bg-emerald-500/20 px-2 py-0.5 text-[11px] font-semibold text-emerald-300 border border-emerald-400/30">
                      <Lock class="size-3" /> SSL 256-Bit
                    </span>
                    <button
                      type="button"
                      :disabled="gatewayStep === 'processing'"
                      class="text-slate-300 hover:text-white transition disabled:opacity-40"
                      @click="closePaymentGateway"
                    >
                      <X class="size-5" />
                    </button>
                  </div>
                </div>

                <!-- Invoice Amount Pill -->
                <div class="mt-3.5 flex items-baseline justify-between rounded-xl bg-white/10 px-3.5 py-2.5 border border-white/15">
                  <div>
                    <span class="text-[11px] text-blue-200 block">প্রদেয় সর্বমোট ফি (Payable Total)</span>
                    <span class="text-xs font-medium text-white truncate max-w-[180px] block">{{ form.fullName }}</span>
                  </div>
                  <div class="text-right">
                    <span class="text-2xl font-black text-amber-300">৳{{ totalFee }}</span>
                  </div>
                </div>
              </div>

              <!-- Gateway Processing View -->
              <div v-if="gatewayStep === 'processing'" class="p-8 text-center space-y-4">
                <div class="mx-auto size-16 flex items-center justify-center rounded-full bg-blue-50 text-blue-600">
                  <div class="size-10 border-4 border-blue-600 border-t-transparent rounded-full animate-spin"></div>
                </div>
                <div>
                  <h4 class="text-base font-bold text-slate-800">নিরাপদ পেমেন্ট প্রসেসিং হচ্ছে...</h4>
                  <p class="mt-1 text-xs text-slate-500">
                    {{ gatewayChannel === 'bkash' ? 'bKash' : gatewayChannel === 'nagad' ? 'Nagad' : 'Secure' }} গেটওয়ের সাথে এনক্রিপ্টেড সংযোগ স্থাপন করা হচ্ছে। অনুগ্রহ করে অপেক্ষা করুন।
                  </p>
                </div>
                <div class="inline-flex items-center gap-1 text-[11px] text-emerald-600 font-semibold bg-emerald-50 px-3 py-1 rounded-full">
                  <ShieldCheck class="size-3.5" /> 100% Secure Transaction
                </div>
              </div>

              <!-- Gateway Success View -->
              <div v-else-if="gatewayStep === 'success'" class="p-8 text-center space-y-3">
                <div class="mx-auto size-16 flex items-center justify-center rounded-full bg-emerald-100 text-emerald-600">
                  <CheckCircle2 class="size-10 text-emerald-600" />
                </div>
                <div>
                  <h4 class="text-lg font-bold text-emerald-800">পেমেন্ট সফল হয়েছে!</h4>
                  <p class="mt-1 font-mono text-xs text-slate-600">
                    Trx ID: <strong class="text-slate-900">{{ form.transactionId }}</strong>
                  </p>
                  <p class="mt-0.5 text-xs text-slate-500">
                    নিবন্ধন সফলভাবে সম্পন্ন হয়েছে, অপেক্ষা করুন...
                  </p>
                </div>
              </div>

              <!-- Gateway Form View -->
              <div v-else class="p-4 sm:p-5">
                <!-- Channel Selector Tabs -->
                <div class="mb-4">
                  <label class="block text-xs font-bold text-slate-700 mb-2">পেমেন্ট মাধ্যম বেছে নিন (Select Channel):</label>
                  <div class="grid grid-cols-4 gap-2">
                    <button
                      v-for="ch in gatewayChannels"
                      :key="ch.id"
                      type="button"
                      class="flex flex-col items-center justify-center rounded-xl border p-2 text-center transition"
                      :class="gatewayChannel === ch.id ? 'border-blue-600 bg-blue-50 font-bold text-blue-900 shadow-xs ring-1 ring-blue-600/30' : 'border-slate-200 bg-white text-slate-600 hover:border-slate-300'"
                      @click="gatewayChannel = ch.id"
                    >
                      <span class="text-xs font-extrabold" :style="{ color: ch.color }">{{ ch.name }}</span>
                      <span class="text-[10px] text-slate-500">{{ ch.bnName }}</span>
                    </button>
                  </div>
                </div>

                <!-- Selected Channel Dynamic UI -->
                <!-- 1. bKash Form -->
                <div v-if="gatewayChannel === 'bkash'" class="rounded-xl border border-pink-200 bg-pink-50/50 p-4 space-y-3">
                  <div class="flex items-center justify-between pb-2 border-b border-pink-200/60">
                    <div class="flex items-center gap-2">
                      <span class="font-extrabold text-sm text-[#E2136E]">bKash Checkout</span>
                    </div>
                    <span class="text-xs font-bold text-[#E2136E]">৳{{ totalFee }}</span>
                  </div>

                  <div>
                    <label class="block text-xs font-semibold text-slate-700 mb-1">
                      বিকাশ অ্যাকাউন্ট নম্বর (bKash Account No.)
                    </label>
                    <input
                      v-model="gatewayAccount"
                      type="tel"
                      placeholder="01XXXXXXXXX"
                      class="block w-full min-h-[42px] rounded-lg border border-slate-300 bg-white px-3 text-sm font-mono text-slate-800 focus:border-[#E2136E] focus:outline-none"
                    />
                  </div>

                  <div>
                    <label class="block text-xs font-semibold text-slate-700 mb-1">
                      বিকাশ পিন নম্বর (Demo PIN)
                    </label>
                    <input
                      v-model="gatewayPin"
                      type="password"
                      placeholder="••••"
                      class="block w-full min-h-[42px] rounded-lg border border-slate-300 bg-white px-3 text-sm font-mono text-slate-800 focus:border-[#E2136E] focus:outline-none"
                    />
                  </div>

                  <label class="flex items-center gap-2 cursor-pointer select-none text-[11px] text-slate-600">
                    <input type="checkbox" v-model="gatewayAgree" class="rounded text-[#E2136E]" />
                    <span>আমি বিকাশ পেমেন্ট শর্তাবলীতে সম্মতি জানাচ্ছি</span>
                  </label>

                  <button
                    type="button"
                    class="w-full flex items-center justify-center gap-2 rounded-xl bg-[#E2136E] hover:bg-[#c2105e] text-white py-2.5 font-bold text-sm transition active:scale-95 shadow-sm"
                    @click="submitGatewayPayment"
                  >
                    <span>Confirm & Pay ৳{{ totalFee }}</span>
                  </button>
                </div>

                <!-- 2. Nagad Form -->
                <div v-else-if="gatewayChannel === 'nagad'" class="rounded-xl border border-orange-200 bg-orange-50/50 p-4 space-y-3">
                  <div class="flex items-center justify-between pb-2 border-b border-orange-200/60">
                    <span class="font-extrabold text-sm text-[#F7941D]">Nagad Payment</span>
                    <span class="text-xs font-bold text-[#F7941D]">৳{{ totalFee }}</span>
                  </div>

                  <div>
                    <label class="block text-xs font-semibold text-slate-700 mb-1">
                      নগদ অ্যাকাউন্ট নম্বর (Nagad Account No.)
                    </label>
                    <input
                      v-model="gatewayAccount"
                      type="tel"
                      placeholder="01XXXXXXXXX"
                      class="block w-full min-h-[42px] rounded-lg border border-slate-300 bg-white px-3 text-sm font-mono text-slate-800 focus:border-[#F7941D] focus:outline-none"
                    />
                  </div>

                  <div>
                    <label class="block text-xs font-semibold text-slate-700 mb-1">
                      নগদ পিন (Demo PIN)
                    </label>
                    <input
                      v-model="gatewayPin"
                      type="password"
                      placeholder="••••"
                      class="block w-full min-h-[42px] rounded-lg border border-slate-300 bg-white px-3 text-sm font-mono text-slate-800 focus:border-[#F7941D] focus:outline-none"
                    />
                  </div>

                  <button
                    type="button"
                    class="w-full flex items-center justify-center gap-2 rounded-xl bg-[#F7941D] hover:bg-[#df7d11] text-white py-2.5 font-bold text-sm transition active:scale-95 shadow-sm"
                    @click="submitGatewayPayment"
                  >
                    <span>Confirm & Pay ৳{{ totalFee }}</span>
                  </button>
                </div>

                <!-- 3. Rocket Form -->
                <div v-else-if="gatewayChannel === 'rocket'" class="rounded-xl border border-purple-200 bg-purple-50/50 p-4 space-y-3">
                  <div class="flex items-center justify-between pb-2 border-b border-purple-200/60">
                    <span class="font-extrabold text-sm text-[#8C3494]">DBBL Rocket</span>
                    <span class="text-xs font-bold text-[#8C3494]">৳{{ totalFee }}</span>
                  </div>

                  <div>
                    <label class="block text-xs font-semibold text-slate-700 mb-1">
                      রকেট মোবাইল অ্যাকাউন্ট নম্বর (12 digits)
                    </label>
                    <input
                      v-model="gatewayAccount"
                      type="tel"
                      placeholder="01XXXXXXXXXX"
                      class="block w-full min-h-[42px] rounded-lg border border-slate-300 bg-white px-3 text-sm font-mono text-slate-800 focus:border-[#8C3494] focus:outline-none"
                    />
                  </div>

                  <button
                    type="button"
                    class="w-full flex items-center justify-center gap-2 rounded-xl bg-[#8C3494] hover:bg-[#742b7b] text-white py-2.5 font-bold text-sm transition active:scale-95 shadow-sm"
                    @click="submitGatewayPayment"
                  >
                    <span>Confirm & Pay ৳{{ totalFee }}</span>
                  </button>
                </div>

                <!-- 4. Card Form -->
                <div v-else class="rounded-xl border border-blue-200 bg-blue-50/50 p-4 space-y-3">
                  <div class="flex items-center justify-between pb-2 border-b border-blue-200/60">
                    <span class="font-extrabold text-sm text-blue-900">Visa / Mastercard / Amex</span>
                    <span class="text-xs font-bold text-blue-900">৳{{ totalFee }}</span>
                  </div>

                  <div>
                    <label class="block text-xs font-semibold text-slate-700 mb-1">কার্ড নম্বর (Demo Card No.)</label>
                    <input
                      type="text"
                      placeholder="4111 2222 3333 4444"
                      value="4111 2222 3333 4444"
                      class="block w-full min-h-[42px] rounded-lg border border-slate-300 bg-white px-3 text-sm font-mono text-slate-800 focus:border-blue-600 focus:outline-none"
                    />
                  </div>

                  <div class="grid grid-cols-2 gap-2">
                    <div>
                      <label class="block text-xs font-semibold text-slate-700 mb-1">মেয়াদ (MM/YY)</label>
                      <input
                        type="text"
                        placeholder="12/28"
                        value="12/28"
                        class="block w-full min-h-[42px] rounded-lg border border-slate-300 bg-white px-3 text-sm font-mono text-slate-800 focus:border-blue-600 focus:outline-none"
                      />
                    </div>
                    <div>
                      <label class="block text-xs font-semibold text-slate-700 mb-1">সিভিভি (CVV)</label>
                      <input
                        type="password"
                        placeholder="123"
                        value="123"
                        class="block w-full min-h-[42px] rounded-lg border border-slate-300 bg-white px-3 text-sm font-mono text-slate-800 focus:border-blue-600 focus:outline-none"
                      />
                    </div>
                  </div>

                  <button
                    type="button"
                    class="w-full flex items-center justify-center gap-2 rounded-xl bg-blue-700 hover:bg-blue-800 text-white py-2.5 font-bold text-sm transition active:scale-95 shadow-sm"
                    @click="submitGatewayPayment"
                  >
                    <Lock class="size-3.5" />
                    <span>Pay Securely ৳{{ totalFee }}</span>
                  </button>
                </div>

                <div class="mt-3 text-center">
                  <button
                    type="button"
                    class="text-xs text-slate-500 hover:text-slate-800 underline"
                    @click="closePaymentGateway"
                  >
                    পেমেন্ট বাতিল করুন (Cancel & Return)
                  </button>
                </div>
              </div>
            </div>
          </div>
        </Teleport>

      </template>
    </div>
  </div>
</template>
