<template>
  <div class="lb-page page-export">
    <div class="page-header">
      <div class="eyebrow">data export</div>
      <h1 class="serif page-title">Export</h1>
    </div>

    <div class="export-card">
      <div class="export-format">
        <span class="export-icon mono">{ }</span>
        <div>
          <strong>JSON export</strong>
          <p>Annotations, labels, document metadata and segment offsets in a single JSON file.</p>
        </div>
      </div>
      <button class="export-btn" @click="download">
        Download annotations.json
      </button>
    </div>
    <p v-if="downloaded" class="export-success mono">Downloaded successfully.</p>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { useArtifact } from '@legalblocks/module-runtime';
const downloaded = ref(false);
const download = async () => {
  const seedData = await $fetch('/seed-data.json');
  let annotations = {};
  try { annotations = (await useArtifact('lb-annotations-' + seedData.doc.id).read()) ?? {}; } catch {}
  const payload = { document: seedData.doc, labels: seedData.labels, annotations };
  const blob = new Blob([JSON.stringify(payload, null, 2)], { type: 'application/json' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url; a.download = 'annotations.json'; a.click();
  URL.revokeObjectURL(url);
  downloaded.value = true;
  setTimeout(() => { downloaded.value = false; }, 3000);
};
</script>

<style scoped>
.page-export { max-width: 600px; margin: 0 auto; padding: 36px 24px; }
.page-header { margin-bottom: 28px; }
.page-title { font-size: 28px; font-weight: 400; margin: 6px 0 0; color: var(--ink); }
.export-card {
  background: var(--white); border: 1px solid var(--line); border-radius: var(--radius);
  padding: 24px; box-shadow: var(--shadow-sm); display: flex; flex-direction: column; gap: 20px;
}
.export-format { display: flex; gap: 16px; align-items: flex-start; }
.export-icon { font-size: 28px; font-weight: 700; color: var(--muted); flex-shrink: 0; padding-top: 2px; }
.export-format p { margin: 4px 0 0; font-size: 13px; color: var(--ink-2); line-height: 1.5; }
.export-btn {
  padding: 10px 20px; background: var(--ink); color: var(--white);
  border: none; border-radius: 8px; font-size: 13px; font-weight: 600;
  cursor: pointer; transition: background 0.15s; font-family: var(--sans);
  align-self: flex-start;
}
.export-btn:hover { background: var(--ink-2); }
.export-success { font-size: 12px; color: var(--green); margin-top: 12px; }
</style>
