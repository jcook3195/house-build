<template>
  <nav class="navbar navbar-dark bg-primary justify-content-center mb-3">
    <h1 class="mb-0 h1 text-white">House Build</h1>
  </nav>
  <div class="container">    
    <div class="row mb-3">
      <div class="col-12">
        <h2>Total Credit from MSB: {{ formatAsCurrency(this.bankCredit) }}</h2>
        <table class="table table-striped table-hover table-bordered">
          <thead>
            <tr class="table-dark">
              <th scope="col"> </th>
              <th scope="col"> </th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <th scope="row" class="text-start">Total Cost so Far: (Materials + Labor)</th>
              <td>{{ formatAsCurrency(totalObj(this.materials) + totalObj(this.labor)) }}</td>
            </tr>
            <tr>
              <th scope="row" class="text-start">Credit From MSB so Far:</th>
              <td>{{ formatAsCurrency(this.bankCredit) }}</td>
            </tr>
            <tr>
              <th scope="row" class="text-start">In the Hole/Ahead: (Diff MSB Credit & Total Cost)</th>
              <td :class="[(this.bankCredit - (totalObj(this.materials) + totalObj(this.labor)))? 'text-danger' : 'text-success']">{{ formatAsCurrency(this.bankCredit - (totalObj(this.materials) + totalObj(this.labor))) }}</td>
            </tr>
            <tr>
              <th scope="row" class="text-start">Unclaimed Credit (Done But No Credit From MSB)</th>
              <td>{{ totalObjectAndFormat(this.unclaimedCredit) }}</td>
            </tr>
            <tr>
              <th scope="row" class="text-start">In the Hole/Ahead After Unclaimed: (Orig In The Hole + Unclaimed Credit)</th>
              <td :class="[(totalObj(this.unclaimedCredit) + (this.bankCredit - (totalObj(this.materials) + totalObj(this.labor))) < 0)? 'text-danger' : 'text-success']">{{ formatAsCurrency(totalObj(this.unclaimedCredit) + (this.bankCredit - (totalObj(this.materials) + totalObj(this.labor)))) }}</td>
            </tr>
          </tbody>
        </table>
      </div>
      <hr class="mb-4 mt-4">
    </div>
    <div class="row mb-3">
      <div class="col-12">
        <h2>MSB Balancing</h2>
        <table class="table table-striped table-hover table-bordered">
          <thead>
            <tr class="table-dark">
              <th scope="col"> </th>
              <th scope="col"> </th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <th scope="row" class="text-start">Total MSB Credit</th>
              <td>{{ formatAsCurrency(this.bankCredit) }}</td>
            </tr>
            <tr>
              <th scope="row" class="text-start">Payouts From MSB</th>
              <td>{{ totalObjectAndFormat(this.paidFromBank) }}</td>
            </tr>
            <tr>
              <th scope="row" class="text-start">MSB Balance (Total Credited - Payouts)</th>
              <td :class="[((this.bankCredit - totalObj(this.paidFromBank)) < 0)? 'text-danger' : 'text-success']">{{ formatAsCurrency((this.bankCredit - totalObj(this.paidFromBank))) }}</td>
            </tr>
            <tr>
              <th scope="row" class="text-start">Planning To Cut Check Soon</th>
              <td>{{ totalObjectAndFormat(this.planToPaySoon) }}</td>
            </tr>
            <tr>
              <th scope="row" class="text-start">MSB Balance After Soon Payments (MSB Balance - Planning To Pay Soon)</th>
              <td :class="[((this.bankCredit - totalObj(this.paidFromBank)) - (totalObj(this.planToPaySoon)) < 0)? 'text-danger' : 'text-success']">{{ formatAsCurrency((this.bankCredit - totalObj(this.paidFromBank)) - (totalObj(this.planToPaySoon))) }}</td>
            </tr>
          </tbody>
        </table>
      </div>
      <hr class="mb-4 mt-4">
    </div>
    <div class="row mb-3">
      <!-- MATERIALS -->
      <div class="col-12 col-md-6">
        <h2>Materials</h2>
        <table class="table table-striped table-hover table-bordered">
          <thead>
            <tr class="table-dark">
              <th scope="col">Expense</th>
              <th scope="col">Cost</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(data, index) in this.materials" :key="index">
              <th scope="row" class="text-start">{{ index }}</th>
              <td>{{ formatAsCurrency(data) }}</td>
            </tr>
          </tbody>
        </table>
        <h3>Materials Total: {{ totalObjectAndFormat(this.materials) }}</h3>
      </div>
      <!-- LABOR -->
      <div class="col-12 col-md-6">
        <h2>Labor</h2>
        <table class="table table-striped table-hover table-bordered">
          <thead>
            <tr class="table-dark">
              <th scope="col">Expense</th>
              <th scope="col">Cost</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(data, index) in this.labor" :key="index">
              <th scope="row" class="text-start">{{ index }}</th>
              <td>{{ formatAsCurrency(data) }}</td>
            </tr>
          </tbody>
        </table>
        <h3>Labor Total: {{ totalObjectAndFormat(this.labor) }}</h3>
      </div>
      <hr class="mb-4 mt-4">
    </div>
    <div class="row mb-3">
      <!-- MATERIALS AND LABOR TOTAL -->
      <div class="col-12">
        <h3>Total Mats & Labor: {{ formatAsCurrency(totalObj(this.materials) + totalObj(this.labor)) }}</h3>
      </div>
      <hr class="mb-4 mt-4">
    </div>
    <div class="row mb-3">
      <!-- OWED -->
      <div class="col-12 col-md-6">
        <h2>Still Owed</h2>
        <table class="table table-striped table-hover table-bordered">
          <thead>
            <tr class="table-dark">
              <th scope="col">Owed</th>
              <th scope="col">Cost</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(data, index) in this.owed" :key="index">
              <th scope="row" class="text-start">{{ index }}</th>
              <td>{{ formatAsCurrency(data) }}</td>
            </tr>
          </tbody>
        </table>
        <h3>Still Owed Total: {{ totalObjectAndFormat(this.owed) }}</h3>
      </div>
      <!-- BANK PAYOUTS -->
      <div class="col-12 col-md-6">
        <h2>Bank Payouts</h2>
        <table class="table table-striped table-hover table-bordered">
          <thead>
            <tr class="table-dark">
              <th scope="col">Payee</th>
              <th scope="col">Cost</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(data, index) in this.paidFromBank" :key="index">
              <th scope="row" class="text-start">{{ index }}</th>
              <td>{{ formatAsCurrency(data) }}</td>
            </tr>
          </tbody>
        </table>
        <h3>Total Bank Payouts: {{ totalObjectAndFormat(this.paidFromBank) }}</h3>
      </div>
      <hr class="mb-4 mt-4">
    </div>
    <div class="row mb-3">
      <!-- UNCLAIMED CREDIT -->
      <div class="col-12 col-md-6">
        <h2>Unclaimed MSB Credit</h2>
        <table class="table table-striped table-hover table-bordered">
          <thead>
            <tr class="table-dark">
              <th scope="col">Expense</th>
              <th scope="col">Cost</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(data, index) in this.unclaimedCredit" :key="index">
              <th scope="row" class="text-start">{{ index }}</th>
              <td>{{ formatAsCurrency(data) }}</td>
            </tr>
          </tbody>
        </table>
        <h3>Unclaimed Credit Total: {{ totalObjectAndFormat(this.unclaimedCredit) }}</h3>
      </div>
      <!-- PLANNING TO PAY SOON -->
      <div class="col-12 col-md-6">
        <h2>Planning To Pay Soon</h2>
        <table class="table table-striped table-hover table-bordered">
          <thead>
            <tr class="table-dark">
              <th scope="col">Expense</th>
              <th scope="col">Cost</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(data, index) in this.planToPaySoon" :key="index">
              <th scope="row" class="text-start">{{ index }}</th>
              <td>{{ formatAsCurrency(data) }}</td>
            </tr>
          </tbody>
        </table>
        <h3>Planning To Pay Soon Total: {{ totalObjectAndFormat(this.planToPaySoon) }}</h3>
      </div>
      <hr class="mb-4 mt-4">
    </div>
    <div class="row mb-3">
      <!-- DRAW SCHEDULE -->
      <div class="col-12">
        <h2>Draws</h2>
        <table class="table table-striped table-hover table-bordered">
          <thead>
            <tr class="table-dark">
              <th scope="col">Draw</th>
              <th scope="col">Percentage</th>
              <th scope="col">Value</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(data, index) in this.draws" :key="index">
              <th scope="row" :class="['text-start small-font', compareSingleDraw(index, this.recievedDraws)? 'green-table-cell' : '']">{{ index }}</th>
              <td :class="[compareSingleDraw(index, this.recievedDraws)? 'green-table-cell' : '']">{{ data }}</td>
              <td :class="[compareSingleDraw(index, this.recievedDraws)? 'green-table-cell' : '']">{{ calcPercentageOfTotalAndFormat(data) }}</td>
            </tr>
          </tbody>
        </table>
        <h3>Credit Line: $185,000</h3>
      </div>
      <hr class="mb-4 mt-4">
    </div>
    <div class="row mb-3">
      <div class="col-12">
        <h3>Paid, But Not Owed (Bundicks): {{ formatAsCurrency(this.paidNotOwed) }}</h3>
      </div>
    </div>
    <div class="row mb-3">
      <div class="col-12">
        <h2>Recieved Draws</h2>
        <table class="table table-striped table-hover table-bordered">
          <thead>
            <tr class="table-dark">
              <th scope="col">Draw</th>
              <th scope="col">Percentage</th>
              <th scope="col">Value</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(data, index) in compareDraws(this.draws, this.recievedDraws)" :key="index">
              <th scope="row" class="text-start small-font">{{ index }}</th>
              <td>{{ data }}</td>
              <td>{{ calcPercentageOfTotalAndFormat(data) }}</td>
            </tr>
          </tbody>
        </table>
        <h3>Total Received: {{ calcPercentageOfTotalAndFormat(totalObj(compareDraws(this.draws, this.recievedDraws))) }}</h3>
        <h3>Total Remaining: {{ formatAsCurrency(185000 - (calcPercentageOfTotal(totalObj(compareDraws(this.draws, this.recievedDraws))))) }}</h3>
      </div>
      <hr class="mb-4 mt-4">
    </div>
    <div class="row mb-3">
      <div class="col-12">
        <h2>Estimated Remaining Expenses</h2>
        <table class="table table-striped table-hover table-bordered">
          <thead>
            <tr class="table-dark">
              <th scope="col">Item</th>
              <th scope="col">Cost</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(data, index) in this.estimatedRemaining" :key="index">
              <th scope="row" class="text-start">{{ index }}</th>
              <td>{{ formatAsCurrency(data) }}</td>
            </tr>
          </tbody>
        </table>
        <h3>Total Estimated: {{ formatAsCurrency(totalObj(this.estimatedRemaining)) }}</h3>
        <h3>Total Still in Bank: {{ formatAsCurrency(185000 - (calcPercentageOfTotal(totalObj(compareDraws(this.draws, this.recievedDraws))))) }}</h3>
        <h3>In the Green: {{ formatAsCurrency((185000 - (calcPercentageOfTotal(totalObj(compareDraws(this.draws, this.recievedDraws))))) - (totalObj(this.estimatedRemaining))) }}</h3>
        <h3>Total Loan Before Down Payment: {{ formatAsCurrency((185000) - ((185000 - (calcPercentageOfTotal(totalObj(compareDraws(this.draws, this.recievedDraws))))) - (totalObj(this.estimatedRemaining)))) }}</h3>
        <h3>Total Cost of Build including not owed: {{ formatAsCurrency((paidNotOwed) + ((185000) - ((185000 - (calcPercentageOfTotal(totalObj(compareDraws(this.draws, this.recievedDraws))))) - (totalObj(this.estimatedRemaining))))) }}</h3>
        <h3>Total Cost of Building including not owed & if pay ourselved back: {{ formatAsCurrency((totalObj(this.owed)) + ((this.paidNotOwed) + ((185000) - ((185000 - (calcPercentageOfTotal(totalObj(compareDraws(this.draws, this.recievedDraws))))) - (totalObj(this.estimatedRemaining)))))) }}</h3>
      </div>
      <hr class="mb-4 mt-4">
    </div>
  </div>
</template>

<script>

export default {
  name: 'HomeView',
  data() {
    return {
      materials: {
        'BH Hall Footer Concrete': 3123,
        'HA Bill Footers & Foundation': 4272,
        'Appliances': 2765,
        'Windows': 3721,
        'Ext Doors': 1837,
        'Shower Fixtures/Pendant Lights': 548,
        'Sink': 350,
        'HA Bill Framing & Roofing': 39591,
        'Paint & Primer': 1080,
        'Cabinets & Countertops': 7720,
        'Insulation': 974,
        'HA Bill Ext Siding': 7600,
        'HA Bill Drywall': 5097,
        'Interior Paint': 591,
        'Interior Doors': 1233,
        'Trim': 427
      },
      labor: {
        'Gene Footers Labor': 2338,
        'Fidel Foundation Labor': 2080,
        'Tyler Electrical Rough': 5000,
        'Roofing Labor': 2000,
        'Jovanny Exterior Siding': 5545,
        'Walter Exterior Painting': 4800,
        'Fidel Stucco Labor': 1000,
        'Mike Rhodes Well': 9400,
        'Beasley Drywall': 6475,
        'Curtis Plumbing': 7532
      },
      owed: {
        'Jordan - Fidel Labor': 2080,
        'Jordan - Windows': 828,
        'Jordan - Ext Doors': 1837,
        'Jordan - Appliances': 2765,
        'Jordan - Sink': 350,
        'Jordan - Handy Andy Bill': 2372,
        'Jordan - Firebox': 635,
        'Jordan - Range Vent': 220,
        'Jordan - Faucets': 220,
        'Blakelyn - Windows': 2893,
        'Jordan - Paint Materials': 1080,
        'Jordan - Fidel Stucco Labor': 1000,
        'Jordan - Insulation Materials': 974,
        'Jordan - Interior Paint': 591,
        'Jordan - Interior Doors': 1233,
        'Jordan - Trim': 427
      },      
      paidFromBank: {
        'Robin - Footers Payback': 5470,
        'Handy Andy Partial Bill': 1900,
        'Tyler Kirkley - Elec Rough': 5000,
        'Handy Andy - August': 39591,
        'Jovanny - Siding Labor': 5545,
        'Bundicks - Roofing Labor': 2000,
        'Walter Flores - Exterior Painting Labor': 4800,
        'Jordan Cook - Cabinets & Countertops': 7720,
        'Handy Andy - September': 7600,
        'Curtis - Plumbing Rough': 7532,
        'Mike Rhodes - Well': 9400,
        'Handy Andy - Oct': 5097,
        'Beasley Drywall': 6475
      },
      planToPaySoon: {
        '-': 0,
      },
      unclaimedCredit: {
        'INTERIOR PAINT :PRIMED': 1850,
        'INTERIOR PAIN/WALL PAPER FINSIH': 3700,
        'CABINETS': 7400,
      },
      paidNotOwed: 12000,
      bankCredit: 94350,
      draws: {
        'FOOTING/WATER METER/WELL': 3,
        'FOUNDATION': 4,
        'FOUNDATION: SLAB': 3,
        'FRAMING: FLOOR/SUBFLOOR': 6,
        'FRAMING: INSIDE/OUTSIDE WALLS': 5,
        'FRAMING: CEILING&WALL SHEATING': 3,
        'FRAMING: ROOF & DECKING': 4,
        'SHINGLES': 3,
        'FIREPLACE CHIMNEY&FLUES': 1,
        'PLUMBING ROUGHED': 2,
        'ELECTRICAL ROUGH': 2,
        'SEPTIC TANK/SEWER': 2,
        'GARAGE DOOR': 1,
        'OUTSIDE DOORS&WINDOWS': 4,
        'EXTERIOR: SIDING': 3,
        'EXTERIOR: BRICK OR STUCCO': 3,
        'NEW - STUCCO': 1,
        'CORNICE & FACIA': 2,
        'INSULATION': 2,
        'SHEETROCK': 6,
        'EXTERIOR:PAINT: PRIMED': 1,
        'EXTERIOR:PAINT: FINISHED': 2,
        'INSIDE TRIM': 4,
        'INTERIOR DOORS': 3,
        'CABINETS': 4,
        'TILE/VINYL: KITCHEN/BATH': 4,
        'FURNACE: ROUGH IN & UNIT SET': 3,
        'HVAC SET': 1,
        'CONCRETE DRIVE/SIDEWALK': 1,
        'INTERIOR PAINT :PRIMED': 1,
        'INTERIOR PAIN/WALL PAPER FINSIH': 2,
        'PLUMBING FIXTURES SET': 2,
        'ELECTRICAL FIXTURES SET': 1,
        'PORCHES & DECKS': 1,
        'FINISHED CARPET': 1,
        'HARDWOOD FLOORS': 2,
        'KITCHEN APPLIANCES': 3,
        'GUTTERS': 1,
        'FINISHED GRADING/LANDSCAPING': 2,
        'FINAL INSPECTION': 1
      },
      recievedDraws: {
        'FOOTING/WATER METER/WELL': 3,
        'FOUNDATION': 4,
        'FOUNDATION: SLAB': 3,
        'FRAMING: FLOOR/SUBFLOOR': 6,
        'FRAMING: INSIDE/OUTSIDE WALLS': 5,
        'FRAMING: CEILING&WALL SHEATING': 3,
        'FRAMING: ROOF & DECKING': 4,
        'OUTSIDE DOORS&WINDOWS': 4,
        'PORCHES & DECKS': 1,
        'KITCHEN APPLIANCES': 3,
        'SHINGLES': 3,
        'PLUMBING ROUGHED': 2,
        'ELECTRICAL ROUGH': 2,
        'EXTERIOR: SIDING': 3,
        'EXTERIOR: BRICK OR STUCCO': 3,
        'CORNICE & FACIA': 2,
        'EXTERIOR:PAINT: PRIMED': 1,
        'EXTERIOR:PAINT: FINISHED': 2,
        'INSULATION': 2,
        'SHEETROCK': 6,
        'NEW - STUCCO': 1,
      },
      estimatedRemaining: {
        'Well': 9400,
        'HVAC': 9800,
        'Electrical Finishing': 3200,
        'Plumb + Tubs': 8500,
        'Trimming': 2000,
        'Septic': 6000,
        'Stairs + Fireplace': 2000,
        'Flooring': 3000,
        'Misc': 1000
      }
    }
  },
  methods: {
    formatAsCurrency(number) {
      const formatter = new Intl.NumberFormat('en-US', {
        style: 'currency',
        currency: 'USD',
      });

      return formatter.format(number);
    },
    totalObj(obj) {
      return Object.values(obj).reduce((a, b) => a + b);
    },
    totalObjectAndFormat(obj) {
      const objTotal = this.totalObj(obj);
      return this.formatAsCurrency(objTotal);
    },
    calcPercentageOfTotal(percentage) {
      const total = 185000;
      const pTotal = (percentage / 100) * total;
      return pTotal;
    },
    calcPercentageOfTotalAndFormat(percentage) {
      const newTotal = this.calcPercentageOfTotal(percentage);
      return this.formatAsCurrency(newTotal);
    },
    compareDraws(obj1, obj2) {
      let newObj = {};

      for(let key1 in obj1) {
        for(let key2 in obj2) {
          if(key1 == key2) {
            newObj[key2] = obj2[key2];
          }
        }
      }

      return newObj;
    },
    compareSingleDraw(drawIndex, recDraws) {
      for(let key in recDraws) {
        if(drawIndex == key) {
          console.log(drawIndex + ' equals ' + key);
          return true;
        } else {
          console.log(drawIndex + ' does not equal ' + key);
        }        
      }

      return false;
    }
  },
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.small-font {
  font-size: .75em !important;
}

.green-table-cell {
  background-color: #95f59c !important;
}
</style>
