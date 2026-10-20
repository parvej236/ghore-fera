<script setup>
import { computed, reactive, ref } from 'vue'

const registrationFee = 100
const isComplete = ref(false)
let nextGuestId = 1

const form = reactive({
  fullName: '',
  quantaId: '',
  gender: '',
  mobileNumber: '',
  occupation: '',
  institution: '',
  tShirtSize: '',
  guests: [],
  paymentMethod: '',
  senderNumber: '',
  transactionId: '',
})

const attendeeCount = computed(() => form.guests.length + 1)
const totalFee = computed(() => attendeeCount.value * registrationFee)

function addGuest() {
  form.guests.push({ id: nextGuestId++, name: '', number: '', gender: '', relation: '' })
}

function removeGuest(id) {
  form.guests = form.guests.filter((guest) => guest.id !== id)
}

function completeRegistration() {
  isComplete.value = true
  window.scrollTo({ top: 0, behavior: 'smooth' })
}
</script>

<template>
  <main class="min-h-screen bg-[#f6f5ef] text-[#172b24] selection:bg-[#c9ef79] selection:text-[#172b24]">
    <div class="mx-auto max-w-7xl px-5 pb-16 pt-6 sm:px-8 lg:px-12">
      <header class="flex items-center justify-between border-b border-[#172b24]/15 pb-5">
        <a href="#top" class="flex items-center gap-3" aria-label="Ghore Fera home">
          <span class="grid size-10 place-items-center rounded-full bg-[#173c30] text-sm font-black tracking-tight text-[#d6f59a]">Q</span>
          <span class="text-sm font-bold tracking-[0.02em]">QUANTA <span class="font-normal text-[#64736c]">REUNION 2027</span></span>
        </a>
        <span class="hidden text-xs font-semibold uppercase tracking-[0.16em] text-[#64736c] sm:block">Registration is open</span>
      </header>

      <section v-if="isComplete" id="top" class="mx-auto flex min-h-[75vh] max-w-2xl flex-col items-center justify-center py-16 text-center">
        <span class="mb-7 grid size-20 place-items-center rounded-full bg-[#d9f4a7] text-4xl text-[#173c30]" aria-hidden="true">✓</span>
        <p class="mb-3 text-xs font-bold uppercase tracking-[0.2em] text-[#557c3d]">Ghore Fera 2027</p>
        <h1 class="font-serif text-4xl font-medium leading-tight sm:text-6xl">Your registration is complete.</h1>
        <p class="mt-5 max-w-lg text-base leading-7 text-[#64736c]">Thank you, {{ form.fullName }}. We’re looking forward to welcoming you back for a day of memories, good food, and familiar faces.</p>
        <div class="mt-9 flex flex-wrap justify-center gap-3 text-sm">
          <span class="rounded-full border border-[#172b24]/15 px-4 py-2">{{ attendeeCount }} {{ attendeeCount === 1 ? 'attendee' : 'attendees' }}</span>
          <span class="rounded-full border border-[#172b24]/15 px-4 py-2">৳{{ totalFee }} registration fee</span>
        </div>
        <button type="button" class="mt-10 text-sm font-semibold underline decoration-[#9aab9f] underline-offset-4 hover:text-[#557c3d]" @click="isComplete = false">Back to registration details</button>
      </section>

      <template v-else>
        <section id="top" class="grid gap-8 pb-10 pt-10 sm:pt-14 lg:grid-cols-[1fr_auto] lg:items-end">
          <div>
            <p class="mb-4 flex items-center gap-2 text-xs font-bold uppercase tracking-[0.2em] text-[#557c3d]"><span class="h-px w-7 bg-[#557c3d]"></span> Quanta Reunion 2027</p>
            <h1 class="max-w-3xl font-serif text-5xl font-medium leading-[1.02] tracking-[-0.035em] sm:text-7xl">Ghore Fera<br /><span class="text-[#698b43]">আনন্দ উৎসব</span></h1>
            <p class="mt-5 max-w-xl text-base leading-7 text-[#64736c]">Come home to the people and moments that made us. Save your place at this year’s reunion.</p>
          </div>
          <div class="flex items-center gap-3 border-l-2 border-[#d6a951] pl-4 lg:mb-2">
            <span class="text-3xl" aria-hidden="true">✳</span>
            <p class="text-sm leading-5"><span class="block font-bold">৳100 per person</span><span class="text-[#64736c]">Registration fee</span></p>
          </div>
        </section>

        <form class="grid items-start gap-10 lg:grid-cols-[minmax(0,1fr)_300px] lg:gap-16" @submit.prevent="completeRegistration">
          <div class="space-y-10">
            <section aria-labelledby="attendee-heading">
              <div class="mb-5 flex items-baseline gap-3 border-b border-[#172b24]/15 pb-3">
                <span class="font-serif text-sm italic text-[#8b9b8e]">01</span>
                <h2 id="attendee-heading" class="text-lg font-bold">Your details</h2>
              </div>
              <div class="grid gap-x-5 gap-y-5 sm:grid-cols-2">
                <label class="sm:col-span-2">
                  <span class="mb-2 block text-sm font-semibold">Full name <span class="text-[#bc553e]">*</span></span>
                  <input v-model.trim="form.fullName" autocomplete="name" required class="form-control" placeholder="As you’d like it on your badge" />
                </label>
                <label>
                  <span class="mb-2 block text-sm font-semibold">Quanta ID <span class="text-[#bc553e]">*</span></span>
                  <input v-model.trim="form.quantaId" required class="form-control" placeholder="Your Quanta ID" />
                </label>
                <label>
                  <span class="mb-2 block text-sm font-semibold">Gender <span class="text-[#bc553e]">*</span></span>
                  <select v-model="form.gender" required class="form-control">
                    <option value="" disabled>Select gender</option><option>Woman</option><option>Man</option><option>Non-binary</option><option>Prefer not to say</option>
                  </select>
                </label>
                <label>
                  <span class="mb-2 block text-sm font-semibold">Mobile number <span class="text-[#bc553e]">*</span></span>
                  <input v-model.trim="form.mobileNumber" type="tel" autocomplete="tel" inputmode="tel" required pattern="01[3-9][0-9]{8}" title="Enter a valid 11-digit Bangladesh mobile number starting with 01" class="form-control" placeholder="01XXXXXXXXX" />
                </label>
                <label>
                  <span class="mb-2 block text-sm font-semibold">T-shirt size <span class="text-[#bc553e]">*</span></span>
                  <select v-model="form.tShirtSize" required class="form-control">
                    <option value="" disabled>Select a size</option><option>XS</option><option>S</option><option>M</option><option>L</option><option>XL</option><option>XXL</option><option>XXXL</option>
                  </select>
                </label>
                <label>
                  <span class="mb-2 block text-sm font-semibold">Occupation <span class="text-[#bc553e]">*</span></span>
                  <input v-model.trim="form.occupation" required class="form-control" placeholder="e.g. Student, Engineer" />
                </label>
                <label>
                  <span class="mb-2 block text-sm font-semibold">Institution <span class="text-[#bc553e]">*</span></span>
                  <input v-model.trim="form.institution" required class="form-control" placeholder="School, university, or workplace" />
                </label>
              </div>
            </section>

            <section aria-labelledby="guests-heading">
              <div class="mb-5 flex items-baseline justify-between gap-4 border-b border-[#172b24]/15 pb-3">
                <div class="flex items-baseline gap-3"><span class="font-serif text-sm italic text-[#8b9b8e]">02</span><h2 id="guests-heading" class="text-lg font-bold">Bringing someone along?</h2></div>
                <span class="text-xs text-[#64736c]">Optional</span>
              </div>
              <div v-if="form.guests.length" class="space-y-5">
                <fieldset v-for="(guest, index) in form.guests" :key="guest.id" class="border-l-2 border-[#d6a951] bg-white/60 p-5 sm:p-6">
                  <legend class="px-2 text-sm font-bold">Guest {{ index + 1 }}</legend>
                  <div class="grid gap-4 sm:grid-cols-2">
                    <label>
                      <span class="mb-2 block text-sm font-semibold">Guest name <span class="text-[#bc553e]">*</span></span>
                      <input v-model.trim="guest.name" required class="form-control" placeholder="Full name" />
                    </label>
                    <label>
                      <span class="mb-2 block text-sm font-semibold">Guest number <span class="text-[#bc553e]">*</span></span>
                      <input v-model.trim="guest.number" type="tel" inputmode="tel" required pattern="01[3-9][0-9]{8}" title="Enter a valid 11-digit Bangladesh mobile number starting with 01" class="form-control" placeholder="01XXXXXXXXX" />
                    </label>
                    <label>
                      <span class="mb-2 block text-sm font-semibold">Guest gender <span class="text-[#bc553e]">*</span></span>
                      <select v-model="guest.gender" required class="form-control"><option value="" disabled>Select gender</option><option>Woman</option><option>Man</option><option>Non-binary</option><option>Prefer not to say</option></select>
                    </label>
                    <label>
                      <span class="mb-2 block text-sm font-semibold">Relation with guest <span class="text-[#bc553e]">*</span></span>
                      <input v-model.trim="guest.relation" required class="form-control" placeholder="e.g. Friend, sibling" />
                    </label>
                  </div>
                  <button type="button" class="mt-4 text-xs font-semibold text-[#a34332] underline underline-offset-4" @click="removeGuest(guest.id)">Remove guest</button>
                </fieldset>
              </div>
              <button type="button" class="mt-5 inline-flex items-center gap-2 border border-[#173c30]/30 px-4 py-3 text-sm font-bold transition hover:border-[#173c30] hover:bg-[#e8eddf]" @click="addGuest"><span class="text-lg leading-none" aria-hidden="true">+</span> Add a guest</button>
            </section>

            <section aria-labelledby="payment-heading">
              <div class="mb-5 flex items-baseline gap-3 border-b border-[#172b24]/15 pb-3">
                <span class="font-serif text-sm italic text-[#8b9b8e]">03</span><h2 id="payment-heading" class="text-lg font-bold">Payment details</h2>
              </div>
              <div class="grid gap-x-5 gap-y-5 sm:grid-cols-2">
                <label>
                  <span class="mb-2 block text-sm font-semibold">Payment method <span class="text-[#bc553e]">*</span></span>
                  <select v-model="form.paymentMethod" required class="form-control"><option value="" disabled>Choose a method</option><option>bKash</option><option>Nagad</option><option>Rocket</option><option>Bank transfer</option><option>Cash</option></select>
                </label>
                <label>
                  <span class="mb-2 block text-sm font-semibold">Number money was sent from <span class="text-[#bc553e]">*</span></span>
                  <input v-model.trim="form.senderNumber" type="tel" inputmode="tel" required class="form-control" placeholder="01XXXXXXXXX" />
                </label>
                <label class="sm:col-span-2">
                  <span class="mb-2 block text-sm font-semibold">Transaction ID <span class="text-[#bc553e]">*</span></span>
                  <input v-model.trim="form.transactionId" required class="form-control" placeholder="Enter your payment transaction ID" />
                </label>
              </div>
            </section>
          </div>

          <aside class="lg:sticky lg:top-6">
            <div class="bg-[#173c30] p-6 text-white sm:p-7">
              <p class="text-xs font-bold uppercase tracking-[0.18em] text-[#c6e58d]">Your registration</p>
              <div class="mt-6 flex items-center justify-between border-b border-white/20 pb-4 text-sm"><span>Attendee</span><span class="max-w-[55%] truncate text-right font-semibold">{{ form.fullName || 'Your name' }}</span></div>
              <div class="flex items-center justify-between border-b border-white/20 py-4 text-sm"><span>Guests</span><span class="font-semibold">{{ form.guests.length }}</span></div>
              <div class="flex items-center justify-between border-b border-white/20 py-4 text-sm"><span>৳{{ registrationFee }} × {{ attendeeCount }} {{ attendeeCount === 1 ? 'person' : 'people' }}</span><span class="font-semibold">৳{{ totalFee }}</span></div>
              <div class="flex items-end justify-between pt-5"><span class="text-sm text-white/75">Total fee</span><span class="font-serif text-4xl">৳{{ totalFee }}</span></div>
              <p class="mt-2 text-xs text-white/60">Registration fee is ৳100 per person.</p>
              <button type="submit" class="mt-7 flex w-full items-center justify-center gap-2 bg-[#d6f59a] px-5 py-4 text-sm font-extrabold text-[#173c30] transition hover:bg-white focus-visible:outline-2 focus-visible:outline-offset-4 focus-visible:outline-[#d6f59a]">Complete registration <span aria-hidden="true">→</span></button>
              <p class="mt-4 text-center text-[11px] leading-5 text-white/55">By submitting, you confirm the details above are correct.</p>
            </div>
            <p class="mt-4 text-xs leading-5 text-[#64736c]">Questions about your registration? Contact the reunion organizing team.</p>
          </aside>
        </form>

        <footer class="mt-16 flex flex-col gap-2 border-t border-[#172b24]/15 pt-5 text-xs text-[#64736c] sm:flex-row sm:items-center sm:justify-between">
          <span>Ghore Fera · আনন্দ উৎসব · Quanta Reunion 2027</span><span>Can’t wait to see you there.</span>
        </footer>
      </template>
    </div>
  </main>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600;700;800&family=Playfair+Display:wght@500;600&display=swap');

:root {
  font-family: 'DM Sans', sans-serif;
  font-synthesis: none;
  text-rendering: optimizeLegibility;
}

.font-serif {
  font-family: 'Playfair Display', Georgia, serif;
}

.form-control {
  width: 100%;
  min-height: 48px;
  border: 1px solid rgb(23 43 36 / 20%);
  border-radius: 2px;
  background: rgb(255 255 255 / 65%);
  padding: 0.75rem 0.875rem;
  color: #172b24;
  font: inherit;
  font-size: 0.875rem;
  outline: none;
  transition: border-color 150ms ease, box-shadow 150ms ease, background 150ms ease;
}

.form-control::placeholder {
  color: #89958d;
}

.form-control:focus {
  border-color: #557c3d;
  background: white;
  box-shadow: 0 0 0 3px rgb(85 124 61 / 14%);
}

@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    scroll-behavior: auto !important;
    transition-duration: 0.01ms !important;
  }
}
</style>
