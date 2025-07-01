<script>
  import { onMount } from "svelte";

  // Convert to runes
  let val = $state();
  let central = $state(true);

  // Derived state
  let centText = $derived(
    central ? "CENTRAL (click to change)" : "ECONOMY (click to change)"
  );

  // Define the cutoff date
  const oldPriceCutoffDate = new Date(2025, 6, 1, 0, 0, 0, 0);

  // Derived state for pricing logic
  let isOldPrice = $derived(val ? new Date(val) < oldPriceCutoffDate : false);
  let priceTypeText = $derived(
    isOldPrice ? "Pricing before July 2025" : "Pricing on or after July 2025"
  );

  // Pricing configuration
  const pricingConfig = {
    old: {
      central: {
        dailyRate: 41,
        getHourlyRate: (hours) => {
          if (hours < 1) return 9;
          if (hours === 1) return 23;
          if (hours === 2) return 28;
          if (hours === 3) return 32;
          if (hours >= 4 && hours <= 7) return 36;
          return 41;
        },
        partialDay: { high: 41, low: 21 },
      },
      nonCentral: {
        dailyRate: 32,
        getHourlyRate: (hours) => {
          if (hours < 1) return 9;
          if (hours === 1) return 22;
          if (hours === 2) return 24;
          if (hours === 3) return 27;
          return 32;
        },
        partialDay: { high: 32, low: 16 },
      },
    },
    new: {
      central: {
        dailyRate: 46,
        getHourlyRate: (hours) => {
          if (hours < 1) return 10;
          if (hours === 1) return 26;
          if (hours === 2) return 31;
          if (hours === 3) return 36;
          if (hours >= 4 && hours <= 7) return 40;
          return 46;
        },
        partialDay: { high: 46, low: 23 },
      },
      nonCentral: {
        dailyRate: 32,
        getHourlyRate: (hours) => {
          if (hours < 1) return 10;
          if (hours === 1) return 26;
          if (hours === 2) return 28;
          if (hours === 3) return 31;
          return 37;
        },
        partialDay: { high: 37, low: 19 },
      },
    },
  };

  function formatDateTimeLocal(date) {
    const year = date.getFullYear();
    const month = (date.getMonth() + 1).toString().padStart(2, "0");
    const day = date.getDate().toString().padStart(2, "0");
    const hours = date.getHours().toString().padStart(2, "0");
    const minutes = date.getMinutes().toString().padStart(2, "0");
    return `${year}-${month}-${day}T${hours}:${minutes}`;
  }

  onMount(() => {
    val = formatDateTimeLocal(new Date());
  });

  // Helper function to calculate time differences
  function getTimeDifference() {
    if (!val) return { days: 0, hours: 0, minutes: 0, milliseconds: -1 };

    const startTime = new Date(val);
    const currentTime = new Date();

    if (isNaN(startTime.getTime()) || isNaN(currentTime.getTime())) {
      return { days: 0, hours: 0, minutes: 0, milliseconds: -1 };
    }

    const milliseconds = currentTime.getTime() - startTime.getTime();
    if (milliseconds < 0)
      return { days: 0, hours: 0, minutes: 0, milliseconds: -1 };

    const totalMinutes = Math.floor(milliseconds / 60000);
    const totalHours = Math.floor(totalMinutes / 60);
    const days = Math.floor(totalHours / 24);
    const hours = totalHours % 24;
    const minutes = totalMinutes % 60;

    return { days, hours, minutes, milliseconds };
  }

  // Calculate derived values - remove the function wrapper
  let timeDiff = $derived(getTimeDifference());
  let days = $derived(timeDiff.days);
  let hours = $derived(timeDiff.hours);
  let minutes = $derived(timeDiff.minutes);

  let price = $derived.by(() => {
    if (timeDiff.milliseconds < 0) return 0;

    const config =
      pricingConfig[isOldPrice ? "old" : "new"][
        central ? "central" : "nonCentral"
      ];

    if (timeDiff.days > 0) {
      let calculatedPrice = timeDiff.days * config.dailyRate;
      calculatedPrice +=
        timeDiff.hours >= 6 ? config.partialDay.high : config.partialDay.low;
      return calculatedPrice;
    } else {
      return config.getHourlyRate(timeDiff.hours);
    }
  });
</script>

<div class="container">
  <div class="price-type-display">
    {priceTypeText}
  </div>
  <button
    class="btn"
    onclick={() => {
      central = !central;
    }}
  >
    {centText}
  </button>
  <input autofocus type="datetime-local" bind:value={val} />
  <div>Price: ${price}</div>
  <div>Days: {days}</div>
  <div>Hours: {hours}</div>
  <div>Minutes: {minutes}</div>
</div>

<style>
  .container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 1em;
  }

  .btn {
    background-color: #4caf50;
    color: white;
    padding: 14px 20px;
    margin: 8px 0;
    border: none;
    cursor: pointer;
  }

  input {
    padding: 10px;
  }

  .price-type-display {
    background-color: #5096eb;
    color: white;
    padding: 5px 20px;
    margin: 8px 0;
    border: none;
    text-align: center;
    font-weight: bold;
    font-size: 1rem;
    box-sizing: border-box;
    height: 30px;
  }

  @media only screen and (max-width: 768px) {
    .container {
      width: 100%;
      height: 100vh;
      font-size: 2em;
    }
    .btn {
      width: 90%;
    }
    input {
      width: 90%;
      height: 4em;
    }
    .price-type-display {
      width: 90%;
    }
  }

  @media (min-width: 767px) {
    .btn {
      width: 50%;
      height: 5em;
    }
    input {
      width: 50%;
      height: 5em;
    }
    .price-type-display {
      width: 50%;
      min-width: 350px;
    }
    * {
      font-size: 1.5em;
    }
  }
</style>
