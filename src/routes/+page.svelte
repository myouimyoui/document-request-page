<script>
  // --- STATE MANAGEMENT (Svelte 5 Runes) ---
  let currentView = $state('login'); // login, dashboard, profile, appointment, review, tracker, history, staff-filter
  let role = $state('alumni'); // 'alumni' or 'staff'
  
  // --- MOCK DATA ---
  const documents = [
    { id: 1, name: 'Transcript of Records (TOR)', price: 250 },
    { id: 2, name: 'Diploma', price: 500 },
    { id: 3, name: 'Certificate of Good Moral Character', price: 150 },
    { id: 4, name: 'Authentication of Documents', price: 100 }
  ];

  let alumniRequests = $state([
    { id: 'REQ-101', doc: 'Transcript of Records (TOR)', status: 'Pending', date: '2026-04-15', time: '10:00 AM' },
    { id: 'REQ-102', doc: 'Diploma', status: 'Approved', date: '2026-04-18', time: '02:00 PM' },
    { id: 'REQ-103', doc: 'Good Moral Character', status: 'Ready for Release', date: '2026-04-11', time: '09:00 AM' }
  ]);

  let transactions = [
    { id: 'TXN-8821', doc: 'Good Moral Character', amount: 150, date: '2026-03-20', status: 'Paid' },
    { id: 'TXN-8822', doc: 'Authentication of Documents', amount: 100, date: '2026-01-15', status: 'Paid' }
  ];

  // Request Form State
  let selectedDoc = $state(null);
  let selectedDate = $state('');
  let selectedTime = $state('');

  // --- FUNCTIONS ---
  function login(selectedRole) {
    role = selectedRole;
    currentView = 'dashboard';
  }

  function logout() {
    role = 'alumni';
    currentView = 'login';
  }

  function navigate(view) {
    currentView = view;
  }

  function proceedToReview() {
    if (selectedDoc && selectedDate && selectedTime) {
      navigate('review');
    } else {
      alert("Please complete all fields.");
    }
  }

  function confirmRequest() {
    alumniRequests = [{
      id: `REQ-${Math.floor(Math.random() * 1000)}`,
      doc: selectedDoc.name,
      status: 'Pending',
      date: selectedDate,
      time: selectedTime
    }, ...alumniRequests];
    alert("Request Submitted Successfully!");
    navigate('dashboard');
  }

  // Staff Filters (Svelte 5 Runes)
  let staffFilter = $state('All');
  let filteredRequests = $derived(staffFilter === 'All' 
    ? alumniRequests 
    : alumniRequests.filter(r => r.status === staffFilter));
</script>

<div class="min-h-screen bg-gray-100 flex items-center justify-center p-4 font-['EB_Garamond',_serif]">

  <main class="w-full max-w-[400px] h-[800px] max-h-[95vh] bg-white rounded-[30px] shadow-2xl overflow-hidden flex flex-col relative border-8 border-gray-900">
    
    {#if currentView !== 'login'}
      <header class="bg-[#1E3A8A] text-white p-5 flex justify-between items-center shrink-0">
        <div class="font-bold text-xl flex items-center gap-2">
          <img src="/AdDU White Seal.png" alt="Logo" class="h-8 w-8 object-contain" />
          Alumni Hub
        </div>
        {#if role === 'staff'}
          <span class="text-xs px-2.5 py-1 rounded-full font-bold font-sans bg-gray-800 text-white">Staff Mode</span>
        {:else}
          <span class="text-xs px-2.5 py-1 rounded-full font-bold font-sans bg-yellow-400 text-[#1E3A8A]">Alumni</span>
        {/if}
      </header>
    {/if}

    <div class="flex-1 overflow-y-auto p-5 pb-24 space-y-6">
      
      {#if currentView === 'login'}
        <div class="flex flex-col items-center justify-center h-full text-center animate-[fadeIn_0.3s_ease-in-out]">
          <img src="/AdDU Blue Seal.png" alt="University Logo" class="h-24 w-auto mx-auto mb-4 object-contain" />
          
          <h2 class="text-2xl font-bold text-gray-800 mb-2">University Portal</h2>
          <h2 class="text-xl font-bold text-gray-800 mb-2">Document Request System</h2>
          <p class="text-gray-500 mb-6 text-lg">Select your role to continue</p>
          
          <div class="flex w-full bg-gray-200 rounded-lg p-1 mb-6">
            <button class="flex-1 py-2.5 rounded-md font-semibold text-lg transition-colors {role === 'alumni' ? 'bg-white text-[#1E3A8A] shadow' : 'text-gray-500 hover:text-gray-700'}" onclick={() => role = 'alumni'}>Alumnus</button>
            <button class="flex-1 py-2.5 rounded-md font-semibold text-lg transition-colors {role === 'staff' ? 'bg-white text-[#1E3A8A] shadow' : 'text-gray-500 hover:text-gray-700'}" onclick={() => role = 'staff'}>Staff</button>
          </div>

          <div class="w-full space-y-4 text-left">
            <div>
              <label class="block text-gray-700 font-semibold mb-1 text-md">University Email</label>
              <input type="email" placeholder="example@university.edu" class="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-[#1E3A8A] font-['EB_Garamond',_serif]" />
            </div>
            <div>
              <label class="block text-gray-700 font-semibold mb-1 text-md">Password</label>
              <input type="password" placeholder="••••••••" class="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-[#1E3A8A]" />
            </div>
          </div>

          <button class="w-full mt-8 p-3.5 bg-[#1E3A8A] text-white rounded-lg font-bold text-xl hover:bg-blue-900 transition-colors active:scale-[0.98]" onclick={() => login(role)}>Login as {role === 'alumni' ? 'Alumnus' : 'Staff'}</button>
          <p class="text-[#1E3A8A] font-semibold mt-4 cursor-pointer hover:underline text-lg">Forgot Password?</p>
        </div>

      {:else if currentView === 'dashboard'}
        <div class="animate-[fadeIn_0.3s_ease-in-out]">
          <h3 class="text-2xl font-bold text-gray-800 mb-4">{role === 'alumni' ? 'My Dashboard' : 'Staff Dashboard'}</h3>
          
          <div class="grid grid-cols-3 gap-3 mb-6">
            <div class="bg-yellow-100 text-yellow-800 p-3 rounded-xl text-center">
              <h4 class="text-sm font-semibold mb-1">Pending</h4>
              <span class="text-2xl font-bold">{alumniRequests.filter(r => r.status === 'Pending').length}</span>
            </div>
            <div class="bg-green-100 text-green-800 p-3 rounded-xl text-center">
              <h4 class="text-sm font-semibold mb-1">Approved</h4>
              <span class="text-2xl font-bold">{alumniRequests.filter(r => r.status === 'Approved').length}</span>
            </div>
            <div class="bg-blue-100 text-blue-800 p-3 rounded-xl text-center">
              <h4 class="text-sm font-semibold mb-1">Ready</h4>
              <span class="text-2xl font-bold">{alumniRequests.filter(r => r.status === 'Ready for Release').length}</span>
            </div>
          </div>

          {#if role === 'alumni'}
            <div class="space-y-3 mb-6">
              <button class="w-full p-3 bg-yellow-400 text-[#1E3A8A] rounded-lg font-bold text-lg active:scale-[0.98] transition-all" onclick={() => navigate('appointment')}>+ New Request</button>
              <button class="w-full p-3 bg-transparent border border-gray-300 text-gray-700 rounded-lg font-bold text-lg active:scale-[0.98] transition-all" onclick={() => navigate('tracker')}>Track Orders</button>
            </div>
          {/if}

          <h4 class="text-xl font-semibold text-gray-800 border-b-2 border-gray-100 pb-2 mb-3">Recent Requests</h4>
          <div class="space-y-3">
            {#each alumniRequests.slice(0, 3) as req}
              <div class="bg-white border border-gray-200 p-4 rounded-xl flex justify-between items-center shadow-sm">
                <div class="flex flex-col">
                  <strong class="text-gray-800">{req.doc}</strong>
                  <span class="text-sm text-gray-500 mt-1">{req.date} at {req.time}</span>
                </div>
                <span class="text-xs px-2.5 py-1 rounded-full font-bold font-sans {req.status === 'Pending' ? 'bg-yellow-100 text-yellow-800' : req.status === 'Approved' ? 'bg-green-100 text-green-800' : 'bg-blue-100 text-blue-800'}">{req.status}</span>
              </div>
            {/each}
          </div>
        </div>

      {:else if currentView === 'appointment'}
        <div class="animate-[fadeIn_0.3s_ease-in-out]">
          <h3 class="text-2xl font-bold text-gray-800 mb-1">Request a Document</h3>
          <p class="text-gray-500 text-lg mb-6">Select document and schedule</p>

          <div class="space-y-5">
            <div>
              <label class="block text-gray-700 font-semibold mb-2 text-md">Select Document</label>
              <div class="grid grid-cols-2 gap-3">
                {#each documents as doc}
                  <button class="border-2 p-3 rounded-xl text-center flex flex-col justify-center items-center h-24 transition-colors font-['EB_Garamond',_serif] {selectedDoc?.id === doc.id ? 'border-[#1E3A8A] bg-blue-50' : 'border-gray-200 hover:bg-gray-50'}" onclick={() => selectedDoc = doc}>
                    <strong class="text-sm text-gray-800 mb-1 leading-tight">{doc.name}</strong>
                    <span class="text-[#28a745] font-bold text-sm">₱{doc.price.toFixed(2)}</span>
                  </button>
                {/each}
              </div>
            </div>
            <div>
              <label class="block text-gray-700 font-semibold mb-1 text-md">Appointment Date</label>
              <input type="date" bind:value={selectedDate} class="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-[#1E3A8A] font-['EB_Garamond',_serif]" />
            </div>
            <div>
              <label class="block text-gray-700 font-semibold mb-1 text-md">Time Slot</label>
              <select bind:value={selectedTime} class="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-[#1E3A8A] font-['EB_Garamond',_serif]">
                <option value="">Select Time</option>
                <option value="09:00 AM">09:00 AM - 10:00 AM</option>
                <option value="10:00 AM">10:00 AM - 11:00 AM</option>
                <option value="01:00 PM">01:00 PM - 02:00 PM</option>
                <option value="03:00 PM">03:00 PM - 04:00 PM</option>
              </select>
            </div>
            <button class="w-full mt-4 p-3.5 bg-[#1E3A8A] text-white rounded-lg font-bold text-lg hover:bg-blue-900 transition-all active:scale-[0.98]" onclick={proceedToReview}>Proceed to Review</button>
          </div>
        </div>

      {:else if currentView === 'review'}
        <div class="animate-[fadeIn_0.3s_ease-in-out]">
          <h3 class="text-2xl font-bold text-gray-800 mb-4">Review Request</h3>
          <div class="bg-gray-50 border border-dashed border-gray-400 p-5 rounded-xl mb-6 shadow-sm">
            <div class="flex justify-between mb-3 text-lg"><span class="text-gray-600">Document:</span><strong class="text-gray-900 text-right w-2/3">{selectedDoc.name}</strong></div>
            <div class="flex justify-between mb-3 text-lg"><span class="text-gray-600">Date:</span><strong class="text-gray-900">{selectedDate}</strong></div>
            <div class="flex justify-between mb-3 text-lg"><span class="text-gray-600">Time:</span><strong class="text-gray-900">{selectedTime}</strong></div>
            <hr class="border-t border-dashed border-gray-400 my-4" />
            <div class="flex justify-between text-xl text-[#1E3A8A] font-bold"><span>Total Price:</span><span>₱{selectedDoc.price.toFixed(2)}</span></div>
          </div>
          <button class="w-full p-3.5 bg-[#1E3A8A] text-white rounded-lg font-bold text-lg hover:bg-blue-900 transition-all active:scale-[0.98]" onclick={confirmRequest}>Confirm & Submit</button>
          <button class="w-full mt-3 p-3.5 bg-transparent border border-gray-300 text-gray-700 rounded-lg font-bold text-lg active:scale-[0.98] transition-all" onclick={() => navigate('appointment')}>Back</button>
        </div>

      {:else if currentView === 'tracker'}
        <div class="animate-[fadeIn_0.3s_ease-in-out]">
          <h3 class="text-2xl font-bold text-gray-800 mb-5">Order Tracker</h3>
          {#each alumniRequests as req}
            <div class="bg-white border border-gray-200 p-4 rounded-xl mb-4 shadow-sm">
              <h4 class="font-bold text-lg text-gray-800 mb-4">{req.doc} <span class="text-sm text-gray-500 font-normal">({req.id})</span></h4>
              <div class="flex justify-between relative font-sans">
                <div class="absolute top-2 left-[15%] right-[15%] h-[2px] bg-gray-200 z-0"></div>
                <div class="relative z-10 w-1/3 flex flex-col items-center">
                  <div class="w-4 h-4 rounded-full border-2 border-white ring-1 mb-1 {req.status === 'Pending' || req.status === 'Approved' || req.status === 'Ready for Release' ? 'bg-green-500 ring-green-500' : 'bg-gray-200 ring-gray-300'}"></div>
                  <span class="text-xs font-semibold {req.status === 'Pending' || req.status === 'Approved' || req.status === 'Ready for Release' ? 'text-green-600' : 'text-gray-400'}">Processed</span>
                </div>
                <div class="relative z-10 w-1/3 flex flex-col items-center">
                  <div class="w-4 h-4 rounded-full border-2 border-white ring-1 mb-1 {req.status === 'Approved' || req.status === 'Ready for Release' ? 'bg-green-500 ring-green-500' : 'bg-gray-200 ring-gray-300'}"></div>
                  <span class="text-xs font-semibold {req.status === 'Approved' || req.status === 'Ready for Release' ? 'text-green-600' : 'text-gray-400'}">Approved</span>
                </div>
                <div class="relative z-10 w-1/3 flex flex-col items-center">
                  <div class="w-4 h-4 rounded-full border-2 border-white ring-1 mb-1 {req.status === 'Ready for Release' ? 'bg-green-500 ring-green-500' : 'bg-gray-200 ring-gray-300'}"></div>
                  <span class="text-xs font-semibold {req.status === 'Ready for Release' ? 'text-green-600' : 'text-gray-400'}">Ready</span>
                </div>
              </div>
            </div>
          {/each}
        </div>

      {:else if currentView === 'history'}
        <div class="animate-[fadeIn_0.3s_ease-in-out]">
          <h3 class="text-2xl font-bold text-gray-800 mb-5">Transaction History</h3>
          <div class="space-y-3">
            {#each transactions as txn}
              <div class="bg-white border border-gray-200 p-4 rounded-xl flex justify-between items-center shadow-sm">
                <div class="flex flex-col"><strong class="text-gray-800 text-lg">{txn.doc}</strong><span class="text-sm text-gray-500 mt-1">{txn.date} | {txn.id}</span></div>
                <div class="flex flex-col items-end"><strong class="text-[#28a745] text-lg font-bold">₱{txn.amount.toFixed(2)}</strong><button class="text-[#1E3A8A] font-bold text-sm mt-1 hover:underline">Receipt</button></div>
              </div>
            {/each}
          </div>
        </div>

      {:else if currentView === 'staff-filter'}
        <div class="animate-[fadeIn_0.3s_ease-in-out]">
          <h3 class="text-2xl font-bold text-gray-800 mb-4">Manage Requests</h3>
          <div class="flex gap-2 mb-5 overflow-x-auto pb-2 scrollbar-hide font-['EB_Garamond',_serif]">
            {#each ['All', 'Pending', 'Approved', 'Ready for Release'] as filterTab}
              <button class="px-4 py-2 rounded-full whitespace-nowrap text-lg font-semibold transition-colors {staffFilter === filterTab ? 'bg-[#1E3A8A] text-white' : 'bg-gray-200 text-gray-600'}" onclick={() => staffFilter = filterTab}>{filterTab === 'Ready for Release' ? 'Ready' : filterTab}</button>
            {/each}
          </div>
          <div class="space-y-3">
            {#each filteredRequests as req}
              <div class="bg-white border border-gray-200 p-4 rounded-xl flex justify-between items-center shadow-sm">
                <div class="flex flex-col"><strong class="text-gray-800 text-lg leading-tight">{req.id} - {req.doc}</strong><span class="text-sm text-gray-500 mt-1">Req: {req.date}</span></div>
                {#if req.status === 'Pending'}
                   <button class="bg-green-500 text-white px-3 py-1.5 rounded text-sm font-bold font-sans hover:bg-green-600 transition-colors">Approve</button>
                {:else}
                   <span class="text-xs px-2.5 py-1 rounded-full font-bold font-sans text-center {req.status === 'Approved' ? 'bg-green-100 text-green-800' : 'bg-blue-100 text-blue-800'}">{req.status}</span>
                {/if}
              </div>
            {/each}
          </div>
        </div>

      {:else if currentView === 'profile'}
        <div class="text-center animate-[fadeIn_0.3s_ease-in-out]">
          <div class="text-6xl bg-gray-100 w-24 h-24 leading-[6rem] rounded-full mx-auto mb-4 border border-gray-200">👤</div>
          <h3 class="text-2xl font-bold text-gray-800 mb-1">{role === 'alumni' ? 'Juan Dela Cruz' : 'Admin Staff'}</h3>
          <p class="text-gray-500 text-lg">{role === 'alumni' ? 'BS Information Technology - 2024' : 'Registrar Office'}</p>
          <div class="mt-8 space-y-4 text-left">
            <div><label class="block text-gray-700 font-semibold mb-1 text-md">Email Address</label><input type="text" value={role === 'alumni' ? 'juan.delacruz@university.edu' : 'staff@university.edu'} disabled class="w-full p-3 border border-gray-300 rounded-lg bg-gray-100 text-gray-500 font-['EB_Garamond',_serif]" /></div>
            <div><label class="block text-gray-700 font-semibold mb-1 text-md">Contact Number</label><input type="text" value="+63 912 345 6789" class="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-[#1E3A8A] font-['EB_Garamond',_serif]" /></div>
          </div>
          <button class="w-full mt-6 p-3.5 bg-[#1E3A8A] text-white rounded-lg font-bold text-lg hover:bg-blue-900 transition-all active:scale-[0.98]">Update Information</button>
          <button class="w-full mt-3 p-3.5 bg-transparent border border-red-500 text-red-500 rounded-lg font-bold text-lg hover:bg-red-50 transition-all active:scale-[0.98]" onclick={logout}>Log Out</button>
        </div>
      {/if}
    </div>

    {#if currentView !== 'login'}
      <nav class="absolute bottom-0 left-0 right-0 h-[75px] bg-white border-t border-gray-200 flex justify-around items-center pb-2 shadow-[0_-5px_10px_rgba(0,0,0,0.03)] font-sans">
        <button class="flex flex-col items-center justify-center w-1/4 pt-2 {currentView === 'dashboard' ? 'text-[#1E3A8A] font-bold' : 'text-gray-500'}" onclick={() => navigate('dashboard')}><span class="text-xl mb-0.5">📊</span><span class="text-xs">Home</span></button>
        {#if role === 'alumni'}
          <button class="flex flex-col items-center justify-center w-1/4 pt-2 {currentView === 'tracker' ? 'text-[#1E3A8A] font-bold' : 'text-gray-500'}" onclick={() => navigate('tracker')}><span class="text-xl mb-0.5">📍</span><span class="text-xs">Tracker</span></button>
          <button class="flex flex-col items-center justify-center w-1/4 pt-2 {currentView === 'history' ? 'text-[#1E3A8A] font-bold' : 'text-gray-500'}" onclick={() => navigate('history')}><span class="text-xl mb-0.5">💳</span><span class="text-xs">History</span></button>
        {:else}
          <button class="flex flex-col items-center justify-center w-1/4 pt-2 {currentView === 'staff-filter' ? 'text-[#1E3A8A] font-bold' : 'text-gray-500'}" onclick={() => navigate('staff-filter')}><span class="text-xl mb-0.5">📑</span><span class="text-xs">Requests</span></button>
        {/if}
        <button class="flex flex-col items-center justify-center w-1/4 pt-2 {currentView === 'profile' ? 'text-[#1E3A8A] font-bold' : 'text-gray-500'}" onclick={() => navigate('profile')}><span class="text-xl mb-0.5">👤</span><span class="text-xs">Profile</span></button>
      </nav>
    {/if}
  </main>
</div>

<style>
  @keyframes fadeIn { 
    from { opacity: 0; transform: translateY(5px); } 
    to { opacity: 1; transform: translateY(0); } 
  }
  .scrollbar-hide::-webkit-scrollbar { display: none; }
  .scrollbar-hide { -ms-overflow-style: none; scrollbar-width: none; }
</style>