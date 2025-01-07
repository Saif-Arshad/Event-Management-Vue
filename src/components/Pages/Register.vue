<script setup>


</script>

<template>
 <div class="font-[sans-serif]  md:h-screen">
     <div class="grid md:grid-cols-2 items-center gap-8 h-full">
          
                  <div class="flex items-center md:p-8 p-6 bg-white md:rounded-tr-[55px] md:rounded-br-[55px] h-full">
                    <form class="max-w-lg w-full mx-auto">
                      <div class="mb-12">
                        <h3 class="text-gray-800 text-2xl sm:text-4xl font-bold">Create Your Account</h3>
                        <p class="text-gray-800 text-sm mt-4 ">Already have an account <a href="#/login" class="text-blue-600 font-semibold hover:underline ml-1 whitespace-nowrap">Login here</a></p>
                      </div>
          
                         <div class="grid grid-cols-1 sm:grid-cols-2 mb-5 gap-x-5">
                      <div>
                        <label class="text-gray-800 text-xs block mb-2">First Name</label>
                       <div class="relative flex items-center">
  <input
    name="first_name"
    type="text"
    required
    class="w-full text-sm border-b border-gray-300 focus:border-gray-800 pl-2 pr-8 py-3 outline-none"
    placeholder="Enter your first name"
  />
  <svg
    xmlns="http://www.w3.org/2000/svg"
    class="w-[26px] h-[26px] absolute right-2"
    fill="#bbb"
    viewBox="0 0 24 24"
  >
    <path
      d="M12 12c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm0 2c-2.67 0-8 1.34-8 4v2h16v-2c0-2.66-5.33-4-8-4z"
    />
  </svg>
</div>

                      </div>
                      <div>
                        <label class="text-gray-800 text-xs block mb-2">Last Name</label>
                        <div class="relative flex items-center">
                          <input name="last_name" type="text" required class="w-full text-sm border-b border-gray-300 focus:border-gray-800 pl-2 pr-8 py-3 outline-none" placeholder="Enter your last name" />
                         <svg
    xmlns="http://www.w3.org/2000/svg"
    class="w-[26px] h-[26px] absolute right-2"
    fill="#bbb"
    viewBox="0 0 24 24"
  >
    <path
      d="M12 12c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm0 2c-2.67 0-8 1.34-8 4v2h16v-2c0-2.66-5.33-4-8-4z"
    />
  </svg>
                        </div>
                      </div>
                      </div>
                      <div>
                        <label class="text-gray-800 text-xs block mb-2">Email</label>
                        <div class="relative flex items-center">
                          <input name="email" type="text" required class="w-full text-sm border-b border-gray-300 focus:border-gray-800 pl-2 pr-8 py-3 outline-none" placeholder="Enter email" />
                          <svg xmlns="http://www.w3.org/2000/svg" fill="#bbb" stroke="#bbb" class="w-[18px] h-[18px] absolute right-2" viewBox="0 0 682.667 682.667">
                            <defs>
                              <clipPath id="a" clipPathUnits="userSpaceOnUse">
                                <path d="M0 512h512V0H0Z" data-original="#000000"></path>
                              </clipPath>
                            </defs>
                            <g clip-path="url(#a)" transform="matrix(1.33 0 0 -1.33 0 682.667)">
                              <path fill="none" stroke-miterlimit="10" stroke-width="40" d="M452 444H60c-22.091 0-40-17.909-40-40v-39.446l212.127-157.782c14.17-10.54 33.576-10.54 47.746 0L492 364.554V404c0 22.091-17.909 40-40 40Z" data-original="#000000"></path>
                              <path d="M472 274.9V107.999c0-11.027-8.972-20-20-20H60c-11.028 0-20 8.973-20 20V274.9L0 304.652V107.999c0-33.084 26.916-60 60-60h392c33.084 0 60 26.916 60 60v196.653Z" data-original="#000000"></path>
                            </g>
                          </svg>
                        </div>
                      </div>
                   
          
                      <div class="mt-8">
                        <label class="text-gray-800 text-xs block mb-2">Password</label>
                        <div class="relative flex items-center">
                          <input name="password" type="password" required class="w-full text-sm border-b border-gray-300 focus:border-gray-800 pl-2 pr-8 py-3 outline-none" placeholder="Enter password" />
                          <svg xmlns="http://www.w3.org/2000/svg" fill="#bbb" stroke="#bbb" class="w-[18px] h-[18px] absolute right-2 cursor-pointer" viewBox="0 0 128 128">
                            <path d="M64 104C22.127 104 1.367 67.496.504 65.943a4 4 0 0 1 0-3.887C1.367 60.504 22.127 24 64 24s62.633 36.504 63.496 38.057a4 4 0 0 1 0 3.887C126.633 67.496 105.873 104 64 104zM8.707 63.994C13.465 71.205 32.146 96 64 96c31.955 0 50.553-24.775 55.293-31.994C114.535 56.795 95.854 32 64 32 32.045 32 13.447 56.775 8.707 63.994zM64 88c-13.234 0-24-10.766-24-24s10.766-24 24-24 24 10.766 24 24-10.766 24-24 24zm0-40c-8.822 0-16 7.178-16 16s7.178 16 16 16 16-7.178 16-16-7.178-16-16-16z" data-original="#000000"></path>
                          </svg>
                        </div>
                      </div>
          
          
                      <div class="mt-12">
                        <button type="button" class="w-full py-3 px-6 text-sm font-semibold tracking-wider rounded-full text-white bg-[#F4683D] hover:bg-[#222] focus:outline-none">
                         Create Account
                        </button>
                      </div>
          
                    
                    </form>
                  </div>
    <div class="relative max-md:order-1 h-full w-full">
  <img 
    src="../../assets/event.jpeg" 
    class="absolute top-0 left-0 w-full h-full object-cover" 
    alt="Register Background"
  />

  <div class="absolute top-0 left-0 w-full h-full bg-black/50"></div>

  <div class="relative z-10 flex flex-col items-center justify-center h-full text-center px-4">
    <h1 class="text-white text-4xl font-bold mb-4">
      Join the World of Events
    </h1>
    <p class="text-white text-lg max-w-lg">
      Create, join, and explore exciting events on Eventitude. Meet like-minded people, share your experiences, and make every moment unforgettable!
    </p>
  </div>
</div>

      </div>
    </div>

</template>
