<template>
  <div class="je-shell">
    <div class="je-header">
      <span class="je-eyebrow">data export</span>
      <h3 class="je-title">JSON Export</h3>
    </div>

    <div class="je-card">
      <div class="je-format-row">
        <span class="je-format-icon">{ }</span>
        <div>
          <strong>annotations.json</strong>
          <p>Spans, labels, document metadata and segment offsets. Gzipped on download.</p>
        </div>
      </div>
      <button class="je-btn" @click="download">
        Download annotations.json
      </button>
      <p v-if="done" class="je-done">Downloaded successfully.</p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { listArtifacts } from '@legalblocks/module-runtime';
const done = ref(false);

const download = async () => {
  let annotations = {};
  try {
    for (const { key, artifact } of await listArtifacts('lb-annotations-')) {
      annotations[key.replace('lb-annotations-', '')] = artifact;
    }
  } catch {}
  const blob = new Blob([JSON.stringify(annotations, null, 2)], { type: 'application/json' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url; a.download = 'annotations.json'; a.click();
  URL.revokeObjectURL(url);
  done.value = true;
  setTimeout(() => { done.value = false; }, 3000);
};
</script>

<style scoped>
.je-shell { font-family: system-ui, sans-serif; padding: 20px; display: flex; flex-direction: column; gap: 16px; }
.je-eyebrow { font-family: monospace; font-size: 10px; font-weight: 500; letter-spacing: 0.14em; text-transform: uppercase; color: #8C8579; display: block; }
.je-title { font-size: 20px; font-weight: 600; margin: 4px 0 0; color: #1A1714; }

.je-card {
  background: #fff; border: 1px solid #E5DFD3; border-radius: 10px;
  padding: 20px; display: flex; flex-direction: column; gap: 16px;
}
.je-format-row { display: flex; gap: 14px; align-items: flex-start; }
.je-format-icon { font-size: 24px; font-family: monospace; font-weight: 700; color: #8C8579; flex-shrink: 0; padding-top: 2px; }
.je-format-row strong { font-size: 14px; color: #1A1714; display: block; }
.je-format-row p { font-size: 12px; color: #6E665A; margin: 4px 0 0; line-height: 1.45; }

.je-btn {
  padding: 9px 18px; background: #1A1714; color: #fff;
  border: none; border-radius: 8px; font-size: 13px; font-weight: 600;
  cursor: pointer; font-family: inherit; transition: background 0.15s; align-self: flex-start;
}
.je-btn:hover { background: #4A453E; }
.je-done { font-size: 12px; color: #5F7A3A; font-family: monospace; margin: 0; }
</style>
