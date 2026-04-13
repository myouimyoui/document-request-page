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

<div class="min-h-screen bg-gray-100 font-['EB_Garamond',_serif] flex flex-col text-gray-800">

  {#if currentView !== 'login'}
    <header class="bg-[#1E3A8A] text-white shadow-md sticky top-0 z-50">
      <div class="max-w-7xl mx-auto px-6 py-4 flex flex-col sm:flex-row justify-between items-center gap-4">
        
        <div class="font-bold text-2xl flex items-center gap-3 shrink-0">
          <img src="/AdDU White Seal.png" alt="Logo" class="h-10 w-10 object-contain" />
          AdDU Alumni
        </div>

        <nav class="flex gap-6 items-center text-lg font-sans font-semibold">
          <button class="transition-colors pb-1 {currentView === 'dashboard' ? 'text-yellow-400 border-b-2 border-yellow-400' : 'text-gray-200 hover:text-white'}" onclick={() => navigate('dashboard')}>Dashboard</button>
          
          {#if role === 'alumni'}
            <button class="transition-colors pb-1 {currentView === 'tracker' ? 'text-yellow-400 border-b-2 border-yellow-400' : 'text-gray-200 hover:text-white'}" onclick={() => navigate('tracker')}>Tracker</button>
            <button class="transition-colors pb-1 {currentView === 'history' ? 'text-yellow-400 border-b-2 border-yellow-400' : 'text-gray-200 hover:text-white'}" onclick={() => navigate('history')}>History</button>
          {:else}
            <button class="transition-colors pb-1 {currentView === 'staff-filter' ? 'text-yellow-400 border-b-2 border-yellow-400' : 'text-gray-200 hover:text-white'}" onclick={() => navigate('staff-filter')}>Requests</button>
          {/if}
          
          <button class="transition-colors pb-1 {currentView === 'profile' ? 'text-yellow-400 border-b-2 border-yellow-400' : 'text-gray-200 hover:text-white'}" onclick={() => navigate('profile')}>Profile</button>
        </nav>

        <div class="shrink-0 hidden md:block">
          {#if role === 'staff'}
            <span class="text-sm px-4 py-1.5 rounded-full font-bold font-sans bg-gray-800 text-white shadow-sm">Staff Mode</span>
          {:else}
            <span class="text-sm px-4 py-1.5 rounded-full font-bold font-sans bg-yellow-400 text-[#1E3A8A] shadow-sm">Alumni</span>
          {/if}
        </div>
      </div>
    </header>
  {/if}

  <main class="flex-1 w-full flex flex-col items-center p-6 sm:p-10">
    
    {#if currentView === 'login'}
      <div class="w-full max-w-lg bg-white rounded-2xl shadow-xl p-10 mt-10 animate-[fadeIn_0.3s_ease-in-out]">
        <div class="flex flex-col items-center text-center">
          <img src="/AdDU Blue Seal.png" alt="University Logo" class="h-28 w-auto mx-auto mb-6 object-contain" />
          
          <h2 class="text-3xl font-bold text-[#1E3A8A] mb-1">University Portal</h2>
          <h2 class="text-xl font-bold text-gray-800 mb-2">Document Request System</h2>
          <p class="text-gray-500 mb-8 text-lg">Select your role to continue</p>
          
          <div class="flex w-full bg-gray-100 rounded-lg p-1.5 mb-8 border border-gray-200">
            <button class="flex-1 py-3 rounded-md font-semibold text-lg transition-colors {role === 'alumni' ? 'bg-white text-[#1E3A8A] shadow-md border border-gray-200' : 'text-gray-500 hover:text-gray-800'}" onclick={() => role = 'alumni'}>Alumnus</button>
            <button class="flex-1 py-3 rounded-md font-semibold text-lg transition-colors {role === 'staff' ? 'bg-white text-[#1E3A8A] shadow-md border border-gray-200' : 'text-gray-500 hover:text-gray-800'}" onclick={() => role = 'staff'}>Staff</button>
          </div>

          <div class="w-full space-y-5 text-left">
            <div>
              <label class="block text-gray-700 font-semibold mb-2 text-md">University Email</label>
              <div class="relative flex items-center border border-gray-300 rounded-lg focus-within:ring-2 focus-within:ring-[#1E3A8A] bg-gray-50">
                <span class="absolute left-4 text-xl text-gray-500">🌐</span>
                <input type="email" placeholder="example@university.edu" class="w-full pl-12 pr-12 p-4 border-none rounded-lg focus:ring-0 bg-transparent font-['EB_Garamond',_serif] text-lg" />
                <span class="absolute right-4 text-xl text-green-500">✔️</span>
              </div>
            </div>
            
            <div>
              <label class="block text-gray-700 font-semibold mb-2 text-md">Password</label>
              <div class="relative flex items-center border border-gray-300 rounded-lg focus-within:ring-2 focus-within:ring-[#1E3A8A] bg-gray-50">
                <span class="absolute left-4 text-xl text-gray-500">🔑</span>
                <input type="password" placeholder="••••••••" class="w-full pl-12 p-4 border-none rounded-lg focus:ring-0 bg-transparent font-['EB_Garamond',_serif] text-lg" />
              </div>
            </div>
          </div>

          <div class="relative flex items-center justify-center font-bold w-full my-8">
            <div class="w-full border-t border-gray-300"></div>
            <span class="absolute bg-white px-4 text-lg font-['EB_Garamond',_serif] text-gray-400">or</span>
          </div>

          <div class="w-full space-y-4">
            <button class="w-full p-4 bg-[#1E3A8A] text-white rounded-lg font-bold text-xl hover:bg-blue-900 transition-colors shadow-md" onclick={() => login(role)}>LOGIN</button>
            <button class="w-full p-4 bg-white border border-gray-300 text-[#1E3A8A] rounded-lg font-bold text-lg flex items-center justify-center gap-3 hover:bg-gray-50 transition-colors shadow-sm">
              <img src="https://authjs.dev/img/providers/google.svg" alt="Google Logo" class="h-6 w-auto" />
              LOGIN VIA GOOGLE
            </button>
          </div>

          <div class="w-full text-lg space-y-2 flex flex-col items-center mt-8">
            <p class="text-[#1E3A8A] font-semibold cursor-pointer hover:underline">Forgot Password?</p>
            <p class="text-[#1E3A8A] font-semibold cursor-pointer hover:underline">Create Account?</p>
          </div>
        </div>
      </div>

    {:else}
      <div class="w-full max-w-5xl bg-white rounded-2xl shadow-sm p-8 md:p-10 border border-gray-200">
        
        {#if currentView === 'dashboard'}
          <div class="animate-[fadeIn_0.3s_ease-in-out]">
            <div class="flex justify-between items-end mb-6 border-b border-gray-100 pb-4">
              <h3 class="text-3xl font-bold text-[#1E3A8A]">{role === 'alumni' ? 'My Dashboard' : 'Staff Dashboard'}</h3>
              {#if role === 'alumni'}
                <div class="flex gap-3">
                  <button class="px-6 py-2.5 bg-transparent border-2 border-[#1E3A8A] text-[#1E3A8A] rounded-lg font-bold hover:bg-blue-50 transition-all" onclick={() => navigate('tracker')}>Track Orders</button>
                  <button class="px-6 py-2.5 bg-yellow-400 text-[#1E3A8A] rounded-lg font-bold hover:bg-yellow-500 transition-all shadow-sm" onclick={() => navigate('appointment')}>+ New Request</button>
                </div>
              {/if}
            </div>
            
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-10">
              <div class="bg-yellow-50 border border-yellow-200 text-yellow-800 p-6 rounded-xl shadow-sm">
                <h4 class="text-lg font-semibold mb-2">Pending Requests</h4>
                <span class="text-4xl font-bold">{alumniRequests.filter(r => r.status === 'Pending').length}</span>
              </div>
              <div class="bg-green-50 border border-green-200 text-green-800 p-6 rounded-xl shadow-sm">
                <h4 class="text-lg font-semibold mb-2">Approved</h4>
                <span class="text-4xl font-bold">{alumniRequests.filter(r => r.status === 'Approved').length}</span>
              </div>
              <div class="bg-blue-50 border border-blue-200 text-blue-800 p-6 rounded-xl shadow-sm">
                <h4 class="text-lg font-semibold mb-2">Ready for Release</h4>
                <span class="text-4xl font-bold">{alumniRequests.filter(r => r.status === 'Ready for Release').length}</span>
              </div>
            </div>

            <h4 class="text-2xl font-semibold text-gray-800 mb-4">Recent Requests</h4>
            <div class="space-y-4">
              {#each alumniRequests.slice(0, 3) as req}
                <div class="bg-white border border-gray-200 p-5 rounded-xl flex justify-between items-center shadow-sm hover:shadow-md transition-shadow">
                  <div class="flex flex-col">
                    <strong class="text-xl text-[#1E3A8A]">{req.doc}</strong>
                    <span class="text-md text-gray-500 mt-1">Requested on: {req.date} at {req.time}</span>
                  </div>
                  <span class="text-sm px-4 py-1.5 rounded-full font-bold font-sans {req.status === 'Pending' ? 'bg-yellow-100 text-yellow-800' : req.status === 'Approved' ? 'bg-green-100 text-green-800' : 'bg-blue-100 text-blue-800'}">{req.status}</span>
                </div>
              {/each}
            </div>
          </div>

        {:else if currentView === 'appointment'}
          <div class="animate-[fadeIn_0.3s_ease-in-out]">
            <h3 class="text-3xl font-bold text-[#1E3A8A] mb-2">Request a Document</h3>
            <p class="text-gray-500 text-xl mb-8 border-b border-gray-100 pb-4">Select your document and schedule a release date.</p>

            <div class="space-y-8 max-w-3xl">
              <div>
                <label class="block text-gray-800 font-bold mb-3 text-lg">1. Select Document</label>
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                  {#each documents as doc}
                    <button class="border-2 p-5 rounded-xl text-left flex justify-between items-center transition-colors font-['EB_Garamond',_serif] {selectedDoc?.id === doc.id ? 'border-[#1E3A8A] bg-blue-50 shadow-sm' : 'border-gray-200 hover:border-gray-300 hover:bg-gray-50'}" onclick={() => selectedDoc = doc}>
                      <strong class="text-lg text-gray-800 leading-tight pr-4">{doc.name}</strong>
                      <span class="text-[#28a745] font-bold text-lg whitespace-nowrap">₱{doc.price.toFixed(2)}</span>
                    </button>
                  {/each}
                </div>
              </div>

              <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div>
                  <label class="block text-gray-800 font-bold mb-3 text-lg">2. Appointment Date</label>
                  <input type="date" bind:value={selectedDate} class="w-full p-4 border border-gray-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-[#1E3A8A] font-sans text-lg bg-gray-50" />
                </div>
                <div>
                  <label class="block text-gray-800 font-bold mb-3 text-lg">3. Time Slot</label>
                  <select bind:value={selectedTime} class="w-full p-4 border border-gray-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-[#1E3A8A] font-sans text-lg bg-gray-50">
                    <option value="">Select a time slot</option>
                    <option value="09:00 AM">09:00 AM - 10:00 AM</option>
                    <option value="10:00 AM">10:00 AM - 11:00 AM</option>
                    <option value="01:00 PM">01:00 PM - 02:00 PM</option>
                    <option value="03:00 PM">03:00 PM - 04:00 PM</option>
                  </select>
                </div>
              </div>

              <div class="pt-6 border-t border-gray-100 flex justify-end">
                <button class="px-8 py-4 bg-[#1E3A8A] text-white rounded-xl font-bold text-xl hover:bg-blue-900 transition-all shadow-md" onclick={proceedToReview}>Proceed to Review →</button>
              </div>
            </div>
          </div>

        {:else if currentView === 'review'}
          <div class="max-w-2xl mx-auto animate-[fadeIn_0.3s_ease-in-out]">
            <h3 class="text-3xl font-bold text-[#1E3A8A] mb-6 text-center">Review Your Request</h3>
            
            <div class="bg-gray-50 border border-gray-200 p-8 rounded-2xl mb-8 shadow-inner">
              <div class="flex justify-between items-center mb-4 text-xl">
                <span class="text-gray-500 font-semibold">Document Selected:</span>
                <strong class="text-gray-900 text-right w-1/2">{selectedDoc.name}</strong>
              </div>
              <div class="flex justify-between items-center mb-4 text-xl">
                <span class="text-gray-500 font-semibold">Appointment Date:</span>
                <strong class="text-gray-900">{selectedDate}</strong>
              </div>
              <div class="flex justify-between items-center mb-6 text-xl">
                <span class="text-gray-500 font-semibold">Time Slot:</span>
                <strong class="text-gray-900">{selectedTime}</strong>
              </div>
              
              <hr class="border-t-2 border-dashed border-gray-300 my-6" />
              
              <div class="flex justify-between items-center text-2xl text-[#1E3A8A] font-bold">
                <span>Total Amount to Pay:</span>
                <span class="text-[#28a745]">₱{selectedDoc.price.toFixed(2)}</span>
              </div>
            </div>

            <div class="flex gap-4">
              <button class="w-1/3 p-4 bg-white border-2 border-gray-300 text-gray-700 rounded-xl font-bold text-lg hover:bg-gray-50 transition-all" onclick={() => navigate('appointment')}>Back to Edit</button>
              <button class="w-2/3 p-4 bg-[#1E3A8A] text-white rounded-xl font-bold text-xl hover:bg-blue-900 transition-all shadow-md" onclick={confirmRequest}>Confirm & Submit Request</button>
            </div>
          </div>

        {:else if currentView === 'tracker'}
          <div class="animate-[fadeIn_0.3s_ease-in-out]">
            <h3 class="text-3xl font-bold text-[#1E3A8A] mb-8 border-b border-gray-100 pb-4">Order Tracker</h3>
            
            <div class="space-y-6">
              {#each alumniRequests as req}
                <div class="bg-white border border-gray-200 p-8 rounded-2xl shadow-sm hover:shadow-md transition-all">
                  <div class="flex justify-between items-center mb-8">
                    <h4 class="font-bold text-2xl text-gray-800">{req.doc}</h4>
                    <span class="text-lg text-gray-500 font-sans bg-gray-100 px-3 py-1 rounded-md border border-gray-200">ID: {req.id}</span>
                  </div>
                  
                  <div class="flex justify-between relative font-sans max-w-3xl mx-auto">
                    <div class="absolute top-4 left-[15%] right-[15%] h-[3px] bg-gray-200 z-0"></div>
                    
                    <div class="relative z-10 w-1/3 flex flex-col items-center">
                      <div class="w-8 h-8 rounded-full border-4 border-white shadow-sm ring-2 mb-3 flex items-center justify-center {req.status === 'Pending' || req.status === 'Approved' || req.status === 'Ready for Release' ? 'bg-green-500 ring-green-500' : 'bg-gray-200 ring-gray-300'}">
                        {#if req.status === 'Pending' || req.status === 'Approved' || req.status === 'Ready for Release'} <span class="text-white text-sm">✓</span> {/if}
                      </div>
                      <span class="text-sm md:text-base font-bold {req.status === 'Pending' || req.status === 'Approved' || req.status === 'Ready for Release' ? 'text-green-700' : 'text-gray-400'}">Request Processed</span>
                    </div>
                    
                    <div class="relative z-10 w-1/3 flex flex-col items-center">
                      <div class="w-8 h-8 rounded-full border-4 border-white shadow-sm ring-2 mb-3 flex items-center justify-center {req.status === 'Approved' || req.status === 'Ready for Release' ? 'bg-green-500 ring-green-500' : 'bg-gray-200 ring-gray-300'}">
                        {#if req.status === 'Approved' || req.status === 'Ready for Release'} <span class="text-white text-sm">✓</span> {/if}
                      </div>
                      <span class="text-sm md:text-base font-bold {req.status === 'Approved' || req.status === 'Ready for Release' ? 'text-green-700' : 'text-gray-400'}">Document Approved</span>
                    </div>
                    
                    <div class="relative z-10 w-1/3 flex flex-col items-center">
                      <div class="w-8 h-8 rounded-full border-4 border-white shadow-sm ring-2 mb-3 flex items-center justify-center {req.status === 'Ready for Release' ? 'bg-green-500 ring-green-500' : 'bg-gray-200 ring-gray-300'}">
                        {#if req.status === 'Ready for Release'} <span class="text-white text-sm">✓</span> {/if}
                      </div>
                      <span class="text-sm md:text-base font-bold {req.status === 'Ready for Release' ? 'text-green-700' : 'text-gray-400'}">Ready for Pickup</span>
                    </div>
                  </div>
                </div>
              {/each}
            </div>
          </div>

        {:else if currentView === 'history'}
          <div class="animate-[fadeIn_0.3s_ease-in-out]">
            <h3 class="text-3xl font-bold text-[#1E3A8A] mb-8 border-b border-gray-100 pb-4">Transaction History</h3>
            
            <div class="overflow-x-auto border border-gray-200 rounded-xl">
              <table class="w-full text-left font-sans border-collapse">
                <thead class="bg-gray-50 border-b border-gray-200 text-gray-700">
                  <tr>
                    <th class="p-4 font-semibold">Transaction ID</th>
                    <th class="p-4 font-semibold">Document</th>
                    <th class="p-4 font-semibold">Date Paid</th>
                    <th class="p-4 font-semibold">Amount</th>
                    <th class="p-4 font-semibold text-right">Action</th>
                  </tr>
                </thead>
                <tbody class="divide-y divide-gray-200">
                  {#each transactions as txn}
                    <tr class="hover:bg-gray-50 transition-colors">
                      <td class="p-4 text-gray-500 font-mono text-sm">{txn.id}</td>
                      <td class="p-4 font-bold text-gray-900">{txn.doc}</td>
                      <td class="p-4 text-gray-600">{txn.date}</td>
                      <td class="p-4 font-bold text-[#28a745]">₱{txn.amount.toFixed(2)}</td>
                      <td class="p-4 text-right">
                        <button class="px-4 py-1.5 border border-[#1E3A8A] text-[#1E3A8A] rounded hover:bg-blue-50 font-bold text-sm transition-colors">View Receipt</button>
                      </td>
                    </tr>
                  {/each}
                </tbody>
              </table>
            </div>
          </div>

        {:else if currentView === 'staff-filter'}
          <div class="animate-[fadeIn_0.3s_ease-in-out]">
            <h3 class="text-3xl font-bold text-[#1E3A8A] mb-6">Manage Document Requests</h3>
            
            <div class="flex flex-wrap gap-3 mb-8 bg-gray-50 p-2 rounded-xl inline-flex font-sans">
              {#each ['All', 'Pending', 'Approved', 'Ready for Release'] as filterTab}
                <button class="px-6 py-2.5 rounded-lg text-md font-bold transition-all {staffFilter === filterTab ? 'bg-[#1E3A8A] text-white shadow-md' : 'text-gray-600 hover:bg-gray-200'}" onclick={() => staffFilter = filterTab}>
                  {filterTab}
                </button>
              {/each}
            </div>

            <div class="overflow-x-auto border border-gray-200 rounded-xl">
              <table class="w-full text-left font-sans border-collapse">
                <thead class="bg-gray-50 border-b border-gray-200 text-gray-700">
                  <tr>
                    <th class="p-4 font-semibold">Request ID</th>
                    <th class="p-4 font-semibold">Document</th>
                    <th class="p-4 font-semibold">Schedule</th>
                    <th class="p-4 font-semibold">Status</th>
                    <th class="p-4 font-semibold text-right">Action</th>
                  </tr>
                </thead>
                <tbody class="divide-y divide-gray-200">
                  {#each filteredRequests as req}
                    <tr class="hover:bg-gray-50 transition-colors">
                      <td class="p-4 text-gray-500 font-mono text-sm">{req.id}</td>
                      <td class="p-4 font-bold text-gray-900 font-['EB_Garamond',_serif] text-lg">{req.doc}</td>
                      <td class="p-4 text-gray-600">{req.date}</td>
                      <td class="p-4">
                        <span class="text-xs px-3 py-1.5 rounded-full font-bold {req.status === 'Pending' ? 'bg-yellow-100 text-yellow-800' : req.status === 'Approved' ? 'bg-green-100 text-green-800' : 'bg-blue-100 text-blue-800'}">{req.status}</span>
                      </td>
                      <td class="p-4 text-right">
                        {#if req.status === 'Pending'}
                           <button class="bg-green-500 text-white px-5 py-2 rounded-lg text-sm font-bold shadow-sm hover:bg-green-600 transition-colors">Approve Request</button>
                        {:else if req.status === 'Approved'}
                           <button class="bg-blue-500 text-white px-5 py-2 rounded-lg text-sm font-bold shadow-sm hover:bg-blue-600 transition-colors">Mark Ready</button>
                        {:else}
                           <button class="bg-gray-200 text-gray-400 px-5 py-2 rounded-lg text-sm font-bold cursor-not-allowed" disabled>Completed</button>
                        {/if}
                      </td>
                    </tr>
                  {/each}
                </tbody>
              </table>
            </div>
          </div>

        {:else if currentView === 'profile'}
          <div class="max-w-3xl mx-auto animate-[fadeIn_0.3s_ease-in-out]">
            <div class="flex items-center gap-8 mb-10 border-b border-gray-100 pb-8">
              <div class="text-7xl bg-gray-100 w-32 h-32 flex items-center justify-center rounded-full border border-gray-200 shadow-sm shrink-0">👤</div>
              <div>
                <h3 class="text-4xl font-bold text-[#1E3A8A] mb-1">{role === 'alumni' ? 'Juan Dela Cruz' : 'Admin Staff'}</h3>
                <p class="text-gray-500 text-xl font-sans">{role === 'alumni' ? 'BS Information Technology - Class of 2024' : 'Office of the University Registrar'}</p>
                <div class="mt-3">
                  <span class="text-xs px-3 py-1 rounded-full font-bold font-sans bg-gray-200 text-gray-700">Account Verified</span>
                </div>
              </div>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-8">
              <div>
                <label class="block text-gray-700 font-bold mb-2 text-lg">University Email Address</label>
                <input type="text" value={role === 'alumni' ? 'juan.delacruz@university.edu' : 'staff@university.edu'} disabled class="w-full p-4 border border-gray-300 rounded-xl bg-gray-100 text-gray-500 font-sans text-lg" />
              </div>
              <div>
                <label class="block text-gray-700 font-bold mb-2 text-lg">Contact Number</label>
                <input type="text" value="+63 912 345 6789" class="w-full p-4 border border-gray-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-[#1E3A8A] font-sans text-lg" />
              </div>
            </div>

            <div class="flex flex-col sm:flex-row gap-4 pt-6 border-t border-gray-100">
              <button class="px-8 py-4 bg-[#1E3A8A] text-white rounded-xl font-bold text-lg hover:bg-blue-900 transition-all shadow-md flex-1">Save Profile Updates</button>
              <button class="px-8 py-4 bg-transparent border-2 border-red-500 text-red-600 rounded-xl font-bold text-lg hover:bg-red-50 transition-all sm:w-1/3" onclick={logout}>Secure Log Out</button>
            </div>
          </div>
        {/if}

      </div>
    {/if}
  </main>
</div>

<style>
  @keyframes fadeIn { 
    from { opacity: 0; transform: translateY(10px); } 
    to { opacity: 1; transform: translateY(0); } 
  }
</style>