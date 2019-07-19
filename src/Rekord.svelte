<script>
import axios from 'axios'
import moment from 'moment'

const columns = ['Klass', 'Gren', 'Namn', 'Vikt', 'Datum']
let records = { herrar: [], damer: [] }
let selectedEvent = 'Allt'

axios.get('https://gkk-styrkelyft.se/rekord/api.php').then(({ data }) => {
  Object.keys(records).forEach(gender => data[gender].forEach(data => {
    records[gender].push({
      Klass: data[1],
      Gren: data[2],
      Namn: data[3].trim(),
      Vikt: data[4],
      Datum: data[5],
    })
  }))

  records = records // Triggers invalidation and re-render in Svelte
}) 

// Sort by selected event, and allow second click to reverse order
let lastClicked = 'Klass'
let sortOrder = 1
function sortBy (column) {
  sortOrder = column === lastClicked ? -sortOrder : 1
  lastClicked = column

  ;['herrar', 'damer'].forEach(gender => {
    records[gender].sort((a, b) => sortOrder * a[column].localeCompare(b[column], undefined, { numeric: true }))
  })

  records = records;
}

// Hightlight records younger than one year
function isNew(person) {
  return moment().diff(person.Datum, 'days') < 365
}
</script>

<style>
#rekord {
  width: 90%;
  margin: 0px auto;
}

thead {
  text-align: left;
  background-color: #253868;
  color: white;
}

td.Klass {width: 12%;}
td.Gren {width: 18%;}
td.Namn {width: 38%;}
td.Vikt {width: 12%;}
td.Datum {width: 20%;}

th, td {padding: 2px;}

th {
    cursor: pointer;
    padding-top: 3px;
    padding-bottom: 3px;
}

tr:nth-child(even){background-color: #f2f2f2;}

.new {
  color: green;
}
</style>

<div id="rekord">
  <select bind:value={selectedEvent}>
    <option>Allt</option>
    <option>Knäböj</option>
    <option>Bänkpress</option>
    <option>Marklyft</option>
    <option>Total</option>
  </select>

  {#each ['Damer', 'Herrar'] as gender}
    <h3 style="margin-bottom: 0px">{ gender }</h3>
    <table>
      <thead>
        <tr>
          {#each columns as column}
            <th on:click={() => sortBy(column)}>
              { column }
              {#if lastClicked === column}
                  <i class="fa fa-arrow-{sortOrder === 1 ? 'up' : 'down'}"></i>
              {/if}
            </th>
          {/each}
        </tr>
      </thead>
      <tbody>
        {#each records[gender.toLowerCase()] as person}
          {#if ['Allt', person.Gren].includes(selectedEvent)}
            <tr class={ isNew(person) ? 'new' : ''}>
              {#each columns as column}
                <td class={column}>{ person[column] }</td>
              {/each}
            </tr>
          {/if}
        {/each}
      </tbody>
    </table>
  {/each}
</div>

