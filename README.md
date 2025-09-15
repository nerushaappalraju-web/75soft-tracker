<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0">
  <title>75 Soft Challenge Tracker</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body { background: linear-gradient(135deg,#00AEEF,#7B42F6); color:white; }
    textarea, input { color:black; }
    .sticker { font-size:1.8rem; }
  </style>
</head>
<body class="p-6">
  <h1 class="text-3xl font-bold mb-4">🏆 75 Soft Challenge Tracker</h1>

  <!-- Settings -->
  <div class="bg-white/20 p-4 rounded-xl mb-6">
    <h2 class="text-xl font-semibold mb-2">Edit Participants & Goal</h2>
    <div id="settings"></div>
    <button id="addUser" class="bg-blue-500 px-3 py-1 rounded mt-2">+ Add Participant</button>
  </div>

  <div id="tracker" class="space-y-6"></div>

  <script>
    const stickers = ["⭐","🌟","🏅","🎉","🔥"];
    let state = JSON.parse(localStorage.getItem("soft75")) || {
      users: [{name:"Nerusha",goal:"Complete workouts"}],
      progress:{}
    };

    function save(){ localStorage.setItem("soft75", JSON.stringify(state)); }

    // Render settings for names and goals
    function renderSettings(){
      const settings = document.getElementById("settings");
      settings.innerHTML = "";
      state.users.forEach((u,i)=>{
        const wrap = document.createElement("div");
        wrap.className="mb-2";
        const nameInput = document.createElement("input");
        nameInput.value=u.name;
        nameInput.className="mr-2 p-1 rounded";
        nameInput.addEventListener("input",()=>{ u.name=nameInput.value; save(); render(); });

        const goalInput = document.createElement("input");
        goalInput.placeholder="Goal";
        goalInput.value=u.goal;
        goalInput.className="p-1 rounded w-60";
        goalInput.addEventListener("input",()=>{ u.goal=goalInput.value; save(); });

        wrap.appendChild(nameInput);
        wrap.appendChild(goalInput);

        // Remove button
        const rm = document.createElement("button");
        rm.textContent="❌";
        rm.className="ml-2";
        rm.addEventListener("click",()=>{
          state.users.splice(i,1); save(); renderSettings(); render();
        });
        wrap.appendChild(rm);

        settings.appendChild(wrap);
      });
    }

    document.getElementById("addUser").addEventListener("click",()=>{
      state.users.push({name:"New",goal:""}); save(); renderSettings(); render();
    });

    function render(){
      const tracker = document.getElementById("tracker");
      tracker.innerHTML="";
      state.users.forEach(u=>{
        const card = document.createElement("div");
        card.className="bg-white/20 rounded-xl p-4";
        const h = document.createElement("h2");
        h.className="text-xl font-bold mb-2";
        h.textContent=`${u.name} – Goal: ${u.goal||"Set your goal"}`;
        card.appendChild(h);

        const grid = document.createElement("div");
        grid.className="grid grid-cols-5 gap-2";
        let completed=0;
        for(let d=1; d<=75; d++){
          const dayDiv = document.createElement("div");
          dayDiv.className="flex flex-col items-center bg-white/10 rounded p-1";
          const box = document.createElement("input");
          box.type="checkbox";
          box.checked = state.progress?.[u.name]?.[d] || false;
          box.addEventListener("change",()=>{
            state.progress[u.name]=state.progress[u.name]||{};
            state.progress[u.name][d]=box.checked; save(); render();
          });
          if(box.checked) completed++;
          dayDiv.appendChild(document.createTextNode(d));
          dayDiv.appendChild(box);
          grid.appendChild(dayDiv);
        }
        card.appendChild(grid);

        if(completed && completed%5===0){
          const st = document.createElement("div");
          st.className="sticker mt-2";
          st.textContent=stickers[Math.floor(Math.random()*stickers.length)];
          card.appendChild(st);
        }
        tracker.appendChild(card);
      });
    }

    renderSettings();
    render();
  </script>
</body>
</html>

