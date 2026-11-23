---
name: amtrak-seat-finder
description: Find Amtrak (Acela) trains with side-by-side Quiet Car seats facing the direction of travel for Brian and his wife, by reading the seat maps on amtrak.com. Use when he asks to find or check train seats for the two of them.
---

# Amtrak seat finder

Find which trains on a given date and route have seats that fit, and report them. Never purchase.

## Preferences (defaults unless the request says otherwise)

- Two adults, seated side by side (A/B or C/D in the same row; B and C are across the aisle and do not count).
- Quiet Car.
- Wife faces the direction of travel. Brian doesn't care which way he faces.
- Flex fare.
- Fallbacks, in order, when the ideal isn't available: (1) both in the Quiet Car but apart, wife facing forward; (2) side by side in the Quiet Car facing backward; (3) side by side facing forward in a regular Business car. Always report which fallbacks exist rather than choosing silently.

If the request doesn't give a date, route, or rough departure time, ask. A time window matters: each train takes a full pass through the booking flow, so check only the 3-4 trains nearest the requested time.

## Why the flow is long

Amtrak shows the seat map only on the Payment page, after Traveler Details. Search results and fare cards say nothing about which seats are open. On Acela, the Quiet Car is a Business class car, so always select Business.

## Procedure

Use Brian's preferred browser on amtrak.com.

1. **Search.** Home page: From and To station codes (e.g. NYP, WAS), depart date, set travelers to 2 adults, Find Trains. Decline non-essential cookies.
2. **List the candidate trains.** The results are paginated 10 per page. The full list is in `sessionStorage.searchresults` under `journeySolutionOption.journeyLegs[0].journeyLegOptions`; each option has `isAcela`, `travelLegs[0].travelService.number`, departure and arrival times, and `reservableAccommodations` with `travelClass`, `fareFamily` (`VLU` = Value, `FLX` = Flex) and per-person price. Use it to get every train, time and Flex price in one read.
3. **For each candidate train:**
   1. On the results page, go to the page holding the train, click its Business fare, choose Flex, click Select. This replaces whatever is in the cart.
   2. Trip Summary: Continue.
   3. Traveler Details: the first time, fill in placeholder details for both travelers (Brian has approved this for seat checking: obviously fake names, an example.com email, a 555 phone number, a placeholder adult birth date) and choose not to join Guest Rewards. The details persist for later trains in the same session. If Brian is signed in to Amtrak in the browser, use his prefilled details instead and enter nothing. Continue.
   4. Payment page: click View/Edit Seats. The map sometimes opens by itself when Amtrak could not auto-assign adjacent seats.
   5. Read the seat map (below). Step with the `>` arrow to the last car; on Acela the Quiet Car is the highest-numbered Business car and its header reads "Quiet Car". Record the other cars too if a fallback is needed.
   6. Close the map and go back to `/tickets/departure.html` for the next train.
4. **Stop at the Payment page.** Do not tick the terms box, enter payment details, or click Purchase.

## Reading the seat map

Each seat is a `span.fc_seat_base` with an `amt-auto-test-id` such as `chair-forward-available-car9-seat15A`. The parts are orientation (`forward` = facing the direction of travel, `rear` = facing backward), status (`available`, `unavailable`, or `selected` for the seats auto-assigned to this session, which count as open), car and seat. The car name is in `.seat-map-header` ("Car 9 | Quiet Car"). Rows with a table between them are four-person table seats, where one row faces forward and the other backward.

Run this in the page for each car, after the car is showing:

```js
(() => {
  const hdr = document.querySelector('.seat-map-header').innerText.replace(/\s+/g, ' ');
  const car = (hdr.match(/\d+ (Car \d+ \| [^<]+?) </) || [])[1] || hdr;
  const st = {};
  document.querySelectorAll('span.fc_seat_base[id^=carnum]').forEach(s => {
    const m = s.getAttribute('amt-auto-test-id').match(/^(.*)-car\d+-seat(\d+)(\w)$/);
    st[m[2] + m[3]] = m[1];
  });
  const open = k => st[k] && /chair-(forward|rear)-(available|selected)$/.test(st[k]);
  const pairs = { forward: [], rear: [] };
  [...new Set(Object.keys(st).map(k => parseInt(k)))].forEach(r =>
    [['A', 'B'], ['C', 'D']].forEach(([x, y]) => {
      if (open(r + x) && open(r + y))
        (st[r + x].includes('forward') ? pairs.forward : pairs.rear).push(r + x + '/' + r + y);
    }));
  const keys = Object.keys(st);
  return JSON.stringify({
    car,
    open: keys.filter(open).length,
    forwardSingles: keys.filter(k => /forward-(available|selected)$/.test(st[k])),
    rearSingles: keys.filter(k => /rear-(available|selected)$/.test(st[k])),
    forwardPairs: pairs.forward,
    rearPairs: pairs.rear
  });
})()
```

Check one result against a screenshot of the map the first time in a session, in case Amtrak has changed the markup.

## Gotchas

- "We've experienced an unknown error" on Traveler Details after several trains: Amtrak is throttling. Retry once, then stop and report the trains already checked and which ones were not.
- Page buttons move between loads; locate Continue, the page numbers and the fare buttons by searching the page rather than reusing coordinates.
- A train with only a couple of seats left may show no Quiet Car availability at all; report it as full rather than skipping it.

## How to present it

Lead with the best train. Then a table with one row per train checked: departure and arrival, train number, Flex price per person, Quiet Car seats open, forward-facing side-by-side pairs (seat numbers), backward-facing pairs. Say when a pair is at a four-person table. List the fallback options for trains without an ideal pair, name any trains that could not be checked, and remind Brian that nothing was bought, that the cart holds placeholder names, and that Amtrak auto-assigns seats so he must move to the Quiet Car seats himself under View/Edit Seats.
