<script setup>

import { Button, Dropdown, FeatherIcon } from 'frappe-ui';
import { computed } from 'vue'
import { stringifyQuery } from 'vue-router';
import ToolsIcon from '@/components/Icons/ToolsIcon.vue'

const props = defineProps({
    doctype: String, 
    docname: String
})

const tools = [
    { 
        label: 'Summarize Meeting', 
        icon: 'briefcase', 
        onClick:() => runSummarizeMeeting()
    },
    { 
        label: 'Map Operations',   
        onClick:() => runMapOperations() 
    },
    { 
        label: 'Create SOW', 
        onClick:() => runCreateSow()
    },
]


function runSummarizeMeeting() {
    const base = "N8N_SUMMARIZE_WEBHOOK"
    const params = URLSearchParams(props.docname)
    window.open(url, '_blank', 'noopener')
}

function runMapOperations() {
    const base = "N8N_MAP_WEBHOOK"
    const params = URLSearchParams(props.docname)
    window.open(url, '_blank', 'noopener')
}

function runCreateSow() {
    const base = "CREATE_SOW_URL"
    const params = URLSearchParams(props.docname)
    window.open(url, '_blank', 'noopener')
}
</script>

<template>
    <Dropdown :options="tools" placement="right">
        <Button label="Tools">
            <template #suffix>
                <ToolsIcon />
            </template>
        </Button>
    </Dropdown>
</template>