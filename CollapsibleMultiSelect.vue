<template>
    <div class="mb-3">
        <label class="form-label d-flex justify-content-between align-items-center">
            <span>{{ label }}</span>

            <div class="d-flex align-items-center gap-2">
                <!-- Show up to 2 selected names -->
                <div v-if="selectedLabels.length" class="d-none d-md-flex flex-wrap gap-1 me-2">
                    <span v-for="(name, index) in displayedLabels" :key="index" class="badge bg-primary text-light">{{
                        name }}</span>
                    <span v-if="remainingCount > 0" class="badge bg-secondary">
                        +{{ remainingCount }}
                    </span>
                </div>
            </div>
            <div class="d-flex align-items-center gap-2">
                <button class="btn btn-sm btn-outline-secondary" type="button" @click="collapsed = !collapsed">
                    <i :class="collapsed ? 'bi bi-chevron-down' : 'bi bi-chevron-up'"></i>
                </button>
            </div>
        </label>
<!--
    <div v-if="collapsed && selectedLabels.length" class="small text-muted mb-2">
        {{ selectedLabels.join(', ') }}
    </div>
-->
        <span v-if="selectedLabels.length" class="text-muted small" :title="selectedLabels.join(', ')">
            {{ selectedLabels.length }} sélectionné
        </span>
        <transition name="fade">
            <div v-show="!collapsed" >
                <input type="text" class="form-control form-control-sm mb-2" v-model="search"
                    :placeholder="`Filtrer ${label.toLowerCase()}...`" />
                <div class="mb-2 d-flex justify-content-between">
                    <button type="button" class="btn btn-sm btn-outline-primary" @click="selectAll">Tout</button>
                    <button type="button" class="btn btn-sm btn-outline-secondary" @click="deselectAll">Rien</button>
                </div>
                <div style="max-height: 200px; overflow-y: auto;" class="p-1">
                    <div class="form-check " v-for="item in filteredOptions" :key="item[valueKey]">
                        <input class="form-check-input me-2" type="checkbox" :id="`${idPrefix}-${item[valueKey]}`"
                            :value="item[valueKey]" v-model="internalValue" />
                        <label class="form-check-label" :for="`${idPrefix}-${item[valueKey]}`">
                            {{ item[labelKey] }}
                        </label>
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>

<script setup>
import { ref, computed, watch, toRefs } from 'vue'
import { nanoid } from 'nanoid'

// Define and destructure props
const props = defineProps({
    modelValue: {
        type: Array,
        required: true
    },
    options: {
        type: Array,
        required: true
    },
    label: {
        type: String,
        default: 'Items'
    },
    valueKey: {
        type: String,
        default: 'id'
    },
    labelKey: {
        type: String,
        default: 'nom'
    },
    idPrefix: {
        type: String,
        default: () => `multi-${nanoid(5)}`
    }
})

const { modelValue, options, labelKey, valueKey } = toRefs(props)

const emit = defineEmits(['update:modelValue'])

// State
const collapsed = ref(true)
const search = ref('')
const internalValue = ref([...modelValue.value])

watch(
    modelValue,
    (val) => {
        if (JSON.stringify(val) !== JSON.stringify(internalValue.value)) {
            internalValue.value = [...val]
        }
    },
    { deep: true }
)

watch(
    internalValue,
    (val) => {
        if (JSON.stringify(val) !== JSON.stringify(modelValue.value)) {
            emit('update:modelValue', val)
        }
    },
    { deep: true }
)

const filteredOptions = computed(() => {
    const term = search.value.toLowerCase()
    return options.value.filter((opt) =>
        String(opt[labelKey.value]).toLowerCase().includes(term)
    )
})

const displayedLabels = computed(() => {
    return selectedLabels.value.slice(0, 2); // show first 2
})

const selectedLabels = computed(() => {
    return options.value
        .filter((opt) => internalValue.value.includes(opt[valueKey.value]))
        .map((opt) => opt[labelKey.value])
})

const remainingCount = computed(() => {
    return selectedLabels.value.length - displayedLabels.value.length;
})

const selectAll = () => {
    internalValue.value = options.value.map((opt) => opt[valueKey.value])
}

const deselectAll = () => {
    internalValue.value = []
}
</script>
<style scoped>
.fade-enter-active,
.fade-leave-active {
    transition: opacity 0.2s ease;
}
.fade-enter-from,
.fade-leave-to {
    opacity: 0;
}
</style>
