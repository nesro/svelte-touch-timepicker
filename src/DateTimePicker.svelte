/*
I tried to edit this element to handle days too, but it's too much work right now. I will use flatpickr for now.
*/

<script>
    import Switcher from './Switcher.svelte';
  
    const DAYS = new Array(30).fill(1).map((v, i) => v + i);
    const HOURS = new Array(24).fill(0).map((v, i) => v + i);
    const MINUTES = new Array(60).fill(0).map((v, i) => v + i);
  
  
    export let time = new Date();
    export let _time, selectedMinute, selectedHour, selectedDay;
    export let visible = false;
    export let classes = '';
  
    let resetTime = (event) => {
      event.stopPropagation()
      time = new Date();
    }
  
    $: {
      _time = time.toLocaleTimeString('cs-CZ', {year: 'numeric', month: 'numeric', day: 'numeric', hour: '2-digit', minute: '2-digit'});
      selectedMinute = +time.toLocaleString('cs-CZ').substring(16, 18)
      selectedHour = +time.toLocaleString('cs-CZ').substring(13, 15)
      selectedDay = time.toLocaleString('cs-CZ').substring(0, 2)
    }
  
    let timeChanged = (event) => {
  
      let {type, changedData} = event.detail;
      let newTime = new Date();
  
      console.log(event.detail)

      if (type === 'minutes') {
        newTime.setMinutes(changedData + 1)
        newTime.setHours(selectedHour)
        newTime.setDate(selectedDay)
      } else if (type === 'hours') {
        newTime.setMinutes(selectedMinute)
        newTime.setHours(changedData + 1)
        newTime.setDate(selectedDay)
      } else if (type === 'days') {
        newTime.setMinutes(selectedMinute)
        newTime.setHours(selectedHour)
        newTime.setDate(changedData + 1)
      }
  
      time = newTime;
    }
  </script>
  
  <style>
  .touch-time-popup{
    z-index: 1;
    position: fixed;
    top:0;
    left:0;
    right:0;
    bottom:0;
    background: rgba(0, 0, 0, 0.3);
    touch-action: pan-down;
  }
  .touch-time-popup > div{
    background: var(--svtt-popup-bg-color, white);
    color: var(--svtt-popup-color, black);
    margin-top: 30vh;
    width: 85%;
    margin-left: 7%;
    border-radius: var(--svtt-popup-radius, 10px);
  }
  .touch-time-wrapper{
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    font-size: var(--svtt-font-size, 20px);
    padding: 1.5rem;
  }
  
  .touch-time-picker {
    display: flex;
    padding: 50px 20px;
    margin: 10px 0;
    overflow: hidden;
  }
  
  .touch-time-reset > button {
    width: 100px;
    height: 30px;
    border-radius: 15px;
    border: var(--svtt-border, 1px solid grey);
    outline: none;
    color: var(--svtt-button-color, black);
    background-color: var(--svtt-button-bg-color, transparent);
    box-shadow: var(--svtt-button-box-shadow, none) ;
    font-weight: 300;
  }
  .touch-time-reset button:nth-child(1):active {
    -webkit-transform: scale(0.95);
            transform: scale(0.95);
  }
  
  .touch-time{
    font-size: 30px;
    font-weight: 300;
  }
  
  </style>
  
  <input type="text" class='{classes}' readonly value={_time} on:focus={() => {visible = !visible}}>
  {#if visible}
    <div class="touch-time-popup" >
      <div>
        <div class="touch-time-wrapper">
            <div class='touch-time'>{_time}</div>
            <div class='touch-time-picker'>
              <Switcher type='days' data={DAYS} selected={selectedDay} on:timeChange={timeChanged} />
              <Switcher type='hours' data={HOURS} selected={selectedHour} on:timeChange={timeChanged} />
              <Switcher type='minutes' data={MINUTES} selected={selectedMinute} on:timeChange={timeChanged}/>
            </div>
          <div class='touch-time-reset'>
            <button on:click={resetTime}>Reset</button>
            <button on:click={() => {visible = !visible}}>Ok</button>
          </div>
        </div>
      </div>
    </div>
  
  
  {/if}
  
  