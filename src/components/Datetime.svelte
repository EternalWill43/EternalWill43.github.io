<script>
    let val;
    let price = 0;
    let days = 0;
    let hours = 0;
    let minutes = 0;
    let central = true;
    let centText = "CENTRAL";
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
                price = days * 38;
                if (hours >= 6) {
                    price += 38;
                } else {
                    price += 19;
                }
            }
            if (days < 1) {
                if (hours < 1) {
                    price = 8;
                } else if (hours == 1) {
                    price = 21;
                } else if (hours == 2) {
                    price = 26;
                } else if (hours == 3) {
                    price = 30;
                } else if (hours >= 4 && hours <= 7) {
                    price = 34;
                } else {
                    price = 38;
                }
            }
        } else {
            if (days > 0) {
                price = days * 29;
                if (hours >= 6) {
                    price += 29;
                } else {
                    price += 15;
                }
            }
            if (days < 1) {
                if (hours < 1) {
                    price = 8;
                } else if (hours == 1) {
                    price = 20;
                } else if (hours == 2) {
                    price = 22;
                } else if (hours == 3) {
                    price = 25;
                } else {
                    price = 29;
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
            centText = central ? "CENTRAL" : "ECONOMY";
        }}
    >
        {centText}
    </button>
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
    .btn {
        background-color: #4caf50;
        color: white;
        padding: 14px 20px;
        margin: 8px 0;
        border: none;
        cursor: pointer;
        width: 50%;
    }
    .container {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
    }
    input {
        width: 50%;
        height: 2em;
    }
</style>
