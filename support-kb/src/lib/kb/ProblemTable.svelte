<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { groupByCause, readAs, uniqueById, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  let { ticket }: { ticket: number } = $props();

  // The desk's own view: each report counted once, under its cause. Reports the desk read as a
  // one-day move stay counted where they are filed and are footnoted under it (desk-notes.json).
  const merged = $derived(uniqueById(reports as Report[]).filter((r) => r.mergedInto === ticket));
  const groups = $derived(groupByCause(merged, tenantOf));
  const wrongTag = (g: { cause: string; reports: Report[] }) => g.reports.filter((r) => r.meta.intakeTag !== g.cause).length;
</script>

<p class="sum"><strong>{merged.length}</strong> reports in #{ticket} · <strong>{groups.length}</strong> problems</p>

<table>
  <thead><tr><th>problem</th><th>reports</th><th>tenants</th><th>intake tagged it differently</th></tr></thead>
  <tbody>
    {#each groups as g (g.cause)}
      <tr><td><code>{g.cause}</code></td><td>{g.reports.length}</td><td>{g.tenants}</td><td>{wrongTag(g)}</td></tr>
    {/each}
  </tbody>
</table>

{#each groups as g (g.cause)}
  {@const moved = readAs(g, notes.oneDayMove)}
  {#if moved}
    <p class="note">Of the {g.reports.length} <code>{g.cause}</code> reports, {moved} read as a one-day move; they are counted here, where they are filed.</p>
  {/if}
{/each}

<style>
  .sum { font-size: 1.1rem; }
  .note { color: var(--muted); font-size: 0.85em; }
</style>
