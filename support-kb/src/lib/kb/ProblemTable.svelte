<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { causeOf, uniqueById, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  let { ticket }: { ticket: number } = $props();

  // Three problems, counted once per report id. A report the desk read as a one-day move is its own
  // problem, not a parser flip, whatever its tenant says (desk-notes.json).
  type Problem = 'parser' | 'hint' | 'one-day shift';
  const rows = $derived.by(() => {
    const merged = uniqueById(reports as Report[]).filter((r) => r.mergedInto === ticket);
    const problemOf = (r: Report): Problem | 'unplaced' =>
      notes.oneDayMove.includes(r.id) ? 'one-day shift' : (causeOf(r, tenantOf(r)) ?? 'unplaced');
    const by: Map<string, Report[]> = new Map();
    for (const r of merged) by.set(problemOf(r), [...(by.get(problemOf(r)) ?? []), r]);
    return {
      total: merged.length,
      groups: [...by.entries()]
        .map(([problem, rs]) => ({
          problem,
          n: rs.length,
          tenants: new Set(rs.map((r) => r.tenant)).size,
          wrongTag: rs.filter((r) => r.meta.intakeTag !== problem).length,
        }))
        .sort((a, b) => b.n - a.n),
    };
  });
</script>

<p class="sum"><strong>{rows.total}</strong> reports in #{ticket} · <strong>{rows.groups.length}</strong> problems</p>

<table>
  <thead><tr><th>problem</th><th>reports</th><th>tenants</th><th>intake tagged it differently</th></tr></thead>
  <tbody>
    {#each rows.groups as g (g.problem)}
      <tr><td><code>{g.problem}</code></td><td>{g.n}</td><td>{g.tenants}</td><td>{g.wrongTag}</td></tr>
    {/each}
  </tbody>
</table>

<style>
  .sum { font-size: 1.1rem; }
</style>
