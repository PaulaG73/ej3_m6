<template>
  <div class="registro">
    <h1 class="registro__title">Registro</h1>

    <div class="registro__grid">
      <form class="registro__form" @submit.prevent="onSubmit">
        <section class="registro__section">
          <h2 class="registro__section-heading">Datos básicos</h2>

          <label class="registro__field">
            <span class="registro__label">Nombre</span>
            <input v-model.trim="nombre" type="text" name="nombre" autocomplete="name" />
          </label>

          <label class="registro__field">
            <span class="registro__label">Edad (0–120)</span>
            <input
              v-model.number="edad"
              type="number"
              name="edad"
              min="0"
              max="120"
              step="1"
            />
          </label>

          <label class="registro__field">
            <span class="registro__label">Biografía</span>
            <textarea v-model.lazy="biografia" name="biografia" rows="4"></textarea>
            <span class="registro__counter">{{ bioLength }} caracteres</span>
          </label>
        </section>

        <section class="registro__section">
          <h2 class="registro__section-heading">Preferencias</h2>

          <fieldset class="registro__fieldset">
            <legend class="registro__legend">Nivel</legend>
            <label v-for="item in niveles" :key="item.value" class="registro__choice">
              <input v-model="nivel" type="radio" name="nivel" :value="item.value" />
              {{ item.label }}
            </label>
          </fieldset>

          <fieldset class="registro__fieldset">
            <legend class="registro__legend">Intereses</legend>
            <label v-for="opt in interesesOpciones" :key="opt" class="registro__choice">
              <input v-model="intereses" type="checkbox" :value="opt" />
              {{ opt }}
            </label>
          </fieldset>

          <label class="registro__field">
            <span class="registro__label">País</span>
            <select v-model="pais" name="pais">
              <option :value="null" disabled>Selecciona un país</option>
              <option v-for="p in paises" :key="p.code" :value="p">
                {{ p.name }}
              </option>
            </select>
          </label>

          <label class="registro__field">
            <span class="registro__label">Tecnologías (Ctrl/Cmd + clic para varias)</span>
            <select v-model="tecnologias" name="tecnologias" multiple size="6">
              <option v-for="tech in tecnologiasOpciones" :key="tech" :value="tech">
                {{ tech }}
              </option>
            </select>
          </label>
        </section>

        <div class="registro__actions">
          <button type="submit" class="registro__submit" :disabled="!isValid">
            Enviar registro
          </button>
        </div>
      </form>

      <aside class="registro__preview" aria-live="polite">
        <h2 class="registro__preview-heading">Vista previa</h2>
        <dl class="registro__preview-list">
          <dt class="registro__preview-term">Nombre</dt>
          <dd class="registro__preview-value">{{ nombrePreview }}</dd>
          <dt class="registro__preview-term">Edad</dt>
          <dd class="registro__preview-value">{{ edadPreview }}</dd>
          <dt class="registro__preview-term">País</dt>
          <dd class="registro__preview-value">{{ paisPreview }}</dd>
          <dt class="registro__preview-term">Nivel</dt>
          <dd class="registro__preview-value">{{ nivelLabel }}</dd>
          <dt class="registro__preview-term">Intereses</dt>
          <dd class="registro__preview-value">{{ interesesPreview }}</dd>
          <dt class="registro__preview-term">Tecnologías</dt>
          <dd class="registro__preview-value">{{ tecnologiasPreview }}</dd>
        </dl>
        <p class="registro__preview-bio">
          Biografía: <strong class="registro__preview-bio-count">{{ bioLength }}</strong> caracteres
        </p>
      </aside>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

/** Paso 1–2: estado enlazado al formulario */
const nombre = ref('')
const edad = ref(null)
const biografia = ref('')
const nivel = ref('junior')
const intereses = ref([])
/** Objeto del listado (misma referencia que en `paises`) para el binding por referencia */
const pais = ref(null)
const tecnologias = ref([])

const niveles = [
  { value: 'junior', label: 'Junior' },
  { value: 'semi-senior', label: 'Semi senior' },
  { value: 'senior', label: 'Senior' },
]

const interesesOpciones = ['Web', 'Mobile', 'DevOps', 'Datos', 'Diseño UX']

const paises = [
  { code: 'CL', name: 'Chile' },
  { code: 'AR', name: 'Argentina' },
  { code: 'PE', name: 'Perú' },
  { code: 'MX', name: 'México' },
]

const tecnologiasOpciones = ['Vue', 'React', 'Angular', 'Svelte', 'Solid']

function edadEsValida(val) {
  const n = typeof val === 'number' ? val : Number(val)
  return Number.isFinite(n) && n >= 0 && n <= 120
}

const bioLength = computed(() => biografia.value.length)

const nivelLabel = computed(
  () => niveles.find((n) => n.value === nivel.value)?.label ?? nivel.value,
)

const isValid = computed(() => {
  const nombreOk = nombre.value.trim().length > 0
  const interesesOk = intereses.value.length >= 1
  return nombreOk && edadEsValida(edad.value) && interesesOk
})

const nombrePreview = computed(() => nombre.value.trim() || '—')
const edadPreview = computed(() =>
  edadEsValida(edad.value) ? String(edad.value) : '—',
)
const paisPreview = computed(() => pais.value?.name ?? '—')
const interesesPreview = computed(() =>
  intereses.value.length ? intereses.value.join(', ') : '—',
)
const tecnologiasPreview = computed(() =>
  tecnologias.value.length ? tecnologias.value.join(', ') : '—',
)

function onSubmit() {
  if (!isValid.value) return

  const payload = {
    nombre: nombre.value.trim(),
    edad: edad.value,
    biografia: biografia.value,
    nivel: nivel.value,
    intereses: [...intereses.value],
    pais: pais.value,
    tecnologias: [...tecnologias.value],
  }

  const json = JSON.stringify(payload, null, 2)
  console.log('Payload JSON:', json)
  alert(json)
}
</script>

<style scoped>
/* Block */
.registro {
  max-width: 52rem;
  margin: 0 auto;
  padding: 1.5rem;
  font-family: system-ui, sans-serif;
  color: #1a1a1a;
}

/* Elements */
.registro__title {
  font-size: 1.5rem;
  margin: 0 0 1rem;
}

.registro__grid {
  display: grid;
  gap: 1.5rem;
  align-items: start;
}

@media (min-width: 840px) {
  .registro__grid {
    grid-template-columns: 1fr 17rem;
  }
}

.registro__section {
  margin-bottom: 1.5rem;
  padding-bottom: 1.25rem;
  border-bottom: 1px solid #ddd;
}

.registro__section:last-of-type {
  border-bottom: none;
}

.registro__section-heading {
  font-size: 1.1rem;
  margin: 0 0 0.75rem;
}

.registro__field {
  display: block;
  margin-bottom: 1rem;
}

.registro__label,
.registro__legend {
  display: block;
  font-weight: 600;
  font-size: 0.875rem;
  margin-bottom: 0.35rem;
}

.registro__field input[type='text'],
.registro__field input[type='number'],
.registro__field textarea,
.registro__field select {
  width: 100%;
  max-width: 100%;
  box-sizing: border-box;
  padding: 0.5rem 0.6rem;
  font: inherit;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.registro__field select[multiple] {
  padding: 0.35rem;
}

.registro__fieldset {
  border: none;
  margin: 0 0 1rem;
  padding: 0;
}

.registro__choice {
  display: inline-flex;
  align-items: center;
  gap: 0.35rem;
  margin-right: 1rem;
  margin-bottom: 0.35rem;
  font-weight: normal;
}

.registro__choice input {
  width: auto;
}

.registro__counter {
  display: block;
  margin-top: 0.35rem;
  font-size: 0.8rem;
  color: #555;
}

.registro__actions {
  margin-top: 1rem;
}

.registro__submit {
  font: inherit;
  padding: 0.6rem 1.1rem;
  border-radius: 6px;
  border: none;
  background: #2563eb;
  color: #fff;
  cursor: pointer;
}

.registro__submit:disabled {
  opacity: 0.45;
  cursor: not-allowed;
}

.registro__submit:not(:disabled):hover {
  background: #1d4ed8;
}

.registro__preview {
  background: #f4f4f5;
  border: 1px solid #e4e4e7;
  border-radius: 8px;
  padding: 1rem 1.1rem;
  font-size: 0.9rem;
}

.registro__preview-heading {
  margin: 0 0 0.75rem;
  font-size: 1rem;
}

.registro__preview-list {
  margin: 0;
}

.registro__preview-term {
  font-weight: 600;
  font-size: 0.75rem;
  text-transform: uppercase;
  letter-spacing: 0.02em;
  color: #52525b;
  margin-top: 0.65rem;
}

.registro__preview-term:first-child {
  margin-top: 0;
}

.registro__preview-value {
  margin: 0.2rem 0 0;
}

.registro__preview-bio {
  margin: 1rem 0 0;
  padding-top: 0.75rem;
  border-top: 1px solid #d4d4d8;
  font-size: 0.85rem;
}

.registro__preview-bio-count {
  font-weight: 700;
}
</style>
