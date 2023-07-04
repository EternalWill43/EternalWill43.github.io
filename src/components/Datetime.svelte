<script>
    let val;
    let price = 0;
    let days = 0;
    let hours = 0;
    let minutes = 0;
    let central = true;
    let centText = "CENTRAL (click to change)";
    function getPrice() {
        let miliseconds = new Date().getTime() - new Date(val).getTime();
        if (miliseconds < 0) return;
        minutes = Math.floor(miliseconds / 60000);
        hours = Math.floor(minutes / 60);
        days = Math.floor(hours / 24);
        hours %= 24;
        minutes %= 60;
        if (central) {
            if (days > 0) {
                price = days * 41;
                if (hours >= 6) {
                    price += 41;
                } else {
                    price += 21;
                }
            }
            if (days < 1) {
                if (hours < 1) {
                    price = 9;
                } else if (hours == 1) {
                    price = 23;
                } else if (hours == 2) {
                    price = 28;
                } else if (hours == 3) {
                    price = 32;
                } else if (hours >= 4 && hours <= 7) {
                    price = 36;
                } else {
                    price = 41;
                }
            }
        } else {
            if (days > 0) {
                price = days * 32;
                if (hours >= 6) {
                    price += 32;
                } else {
                    price += 16;
                }
            }
            if (days < 1) {
                if (hours < 1) {
                    price = 9;
                } else if (hours == 1) {
                    price = 22;
                } else if (hours == 2) {
                    price = 24;
                } else if (hours == 3) {
                    price = 27;
                } else {
                    price = 32;
                }
            }
        }
    }
</script>

<div class="container">
    <button
        class="btn"
        on:click={() => {
            central = !central;
            centText = central
                ? "CENTRAL (click to change)"
                : "ECONOMY (click to change)";
        }}
    >
        {centText}
    </button>
    <!-- svelte-ignore a11y-autofocus -->
    <input
        autofocus
        type="datetime-local"
        on:change={(e) => {
            val = e.target.value;
        }}
    />
    <button class="btn" on:click={() => getPrice()}>CALCULATE</button>
    <div>Price: ${price}</div>
    <div>Days: {days}</div>
    <div>Hours: {hours}</div>
    <div>Minutes: {minutes}</div>
</div>

<style>
    @media only screen and (max-width: 768px) {
        .btn {
            background-color: #4caf50;
            color: white;
            padding: 14px 20px;
            margin: 8px 0;
            border: none;
            cursor: pointer;
            width: 100vw;
        }
        .btn:nth-of-type(2) {
            height: 20%;
        }
        .container {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            width: 100%;
            height: 100vh;
            font-size: 2em;
        }
        input {
            width: 100%;
            height: 4em;
        }
    }
    @media (min-width: 767px) {
        .btn {
            background-color: #4caf50;
            color: white;
            padding: 14px 20px;
            margin: 8px 0;
            border: none;
            cursor: pointer;
            width: 50%;
            height: 5em;
        }
        .container {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }
        input {
            width: 50%;
            height: 5em;
        }
        * {
            font-size: 1.5em;
        }
    }
</style>
