<template>
    <div v-if="document.loading" class="p-4">Loading...</div>
    <div v-else-if="document.doc">
        <Button 
            label="Generate"
            @click="generateQuestionnaire"
            :loading="generating" 
            loading-text="Generating..."
        />
        <div v-html="document.doc.custom_questionnaires" class="rich-html rounded-lg border border-outline-gray-2 bg-surface-white p-4 text-base
  text-ink-gray-8 shadow-sm" />
    </div>
</template>
<script setup>
import { watch, computed, ref } from 'vue'
import { useDocument } from '@/data/document'
import DOMPurify from 'dompurify'
import { Button } from 'frappe-ui'
import {createDialog} from '@/utils/dialogs'

const showDialog = ref(false)
const message = ref("")
const generating = ref(false)

const props = defineProps({
    doctype: String,
    docname: String,
})

const { document } = useDocument(props.doctype, props.docname)
const html = computed(() => DOMPurify.sanitize(document.doc?.custom_questionnaires || ''))

async function generateQuestionnaire() {
    const base = "N8N_WEBHOOK_URL"
    const params = new URLSearchParams({ leadId:props.docname, doctype:props.doctype })
    const url = `${url}?${params}`

    await fetch(url)
        setLoading(true)
        .then(resp => {
            if (resp.ok){
                message.value = `Succesfully generated questionnaire for ${props.docname}`               
            }
            else {
                message.value = `HTTP Error : ${resp.status}`
            }
            createDialog({
                title: "Questionnaire generation", 
                messageo:message.value, 
                actions: [
                    {
                        label: 'Close', variant:'solid', theme:"blue", 
                        onClick: ({ close }) => { close() }
                    },
                ]
            })
        })
        .catch(err => {
            message.value = `Generation met an error : ${error}`
            createDialog({
                title: "Questionnaire generation", 
                message:message.value, 
                actions: [
                    {
                        label: 'Close', variant:'solid', theme:"red", 
                        onClick: ({ close }) => { close() }
                    },
                ]
            })
        })
        .finally(() => setLoading(false))

}

function setLoading(loading) {
    if (loading) {
        generating.value=true
    }
    else {
        geenrating.value=false
    }
}
</script>

<style scoped>
/* :deep() is required — v-html content has no scope attribute, */
/* so scoped rules won't reach it otherwise.                     */
.rich-html :deep(h1),
.rich-html :deep(h2),
.rich-html :deep(h3) {
    font-weight: 600;
    color: var(--ink-gray-9, #171717);
    margin-bottom: 0.5rem;
    line-height: 1.4;
}

.rich-html :deep(h1) {
    font-size: 1.125rem;
}

.rich-html :deep(h2) {
    font-size: 1rem;
}

.rich-html :deep(p) {
    margin-bottom: 0.5rem;
}

.rich-html :deep(p:last-child) {
    margin-bottom: 0;
}

.rich-html :deep(ul) {
    list-style: disc;
    padding-left: 1.25rem;
    margin-bottom: 0.5rem;
}

.rich-html :deep(ol) {
    list-style: decimal;
    padding-left: 1.25rem;
    margin-bottom: 0.5rem;
}

.rich-html :deep(li) {
    margin-bottom: 0.25rem;
}

.rich-html :deep(a) {
    color: var(--ink-blue-link, #2563eb);
    text-decoration: underline;
}

.rich-html :deep(strong) {
    font-weight: 600;
    color: var(--ink-gray-9, #171717);
}

.rich-html :deep(img) {
    max-width: 100%;
    border-radius: 0.375rem;
}

.rich-html :deep(hr) {
    border-color: var(--outline-gray-2, #e5e7eb);
    margin: 0.75rem 0;
}

.rich-html :deep(blockquote) {
    border-left: 3px solid var(--outline-gray-3, #d1d5db);
    padding-left: 0.75rem;
    color: var(--ink-gray-6, #6b7280);
}
</style>
