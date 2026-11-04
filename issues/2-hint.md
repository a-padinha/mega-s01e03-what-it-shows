# booking-v3 form hint says MM/DD/YYYY to day-first tenants

**Reports covered: 33** (from #4471, counted once per report id)

Through booking-v3 the date field prints `MM/DD/YYYY`. People at day-first tenants (alder.physio, copper.studio, pale.harbour) type what the hint says, and the form reads it their way. A parser fix does not change this. All 33 reports came in through the form channel. Intake tagged 15 of them as `parser`.

## Report ids

R-41230, R-41241, R-41251, R-41259, R-41262, R-41275, R-41294, R-41305, R-41308, R-41366, R-41372, R-41384, R-41387, R-41396, R-41400, R-41406, R-41415, R-41416, R-41418, R-41428, R-41439, R-41441, R-41449, R-41453, R-41456, R-41460, R-41472, R-41485, R-41493, R-41539, R-41572, R-41582, R-41584
