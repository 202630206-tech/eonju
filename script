const jsConfetti = new JSConfetti();
let selectedAvatarData = '🦊';
let currentFeedFilter = 'all';

let isDeveloperMode = localStorage.getItem('gomin_is_dev') === 'true';

// LocalStorage Helper Functions
function getUsers() {
  return JSON.parse(localStorage.getItem('gomin_users') || '{}');
}
function setUsers(users) {
  localStorage.setItem('gomin_users', JSON.stringify(users));
}
function getCurrentUser() {
  return localStorage.getItem('gomin_current_user');
}
function setCurrentUser(nickname) {
  localStorage.setItem('gomin_current_user', nickname);
}
function getPosts() {
  return JSON.parse(localStorage.getItem('gomin_posts') || '[]');
}
function setPosts(posts) {
  localStorage.setItem('gomin_posts', JSON.stringify(posts));
}

// App Initialization
function initApp() {
  const currentUser = getCurrentUser();
  const users = getUsers();

  if (!currentUser || !users[currentUser]) {
    openProfileModal(false);
  } else {
    updateHeaderUI();
    handleHashRoute();
  }
}

// 구글/브라우저 자체 뒤로가기 연동
function navigateTo(tab) {
  location.hash = tab;
}

function handleHashRoute() {
  const hash = location.hash.replace('#', '') || 'lobby';
  renderTabUI(hash);
}

window.addEventListener('hashchange', handleHashRoute);

function renderTabUI(tab) {
  const lobbySec = document.getElementById('lobbySection');
  const feedSec = document.getElementById('feedSection');
  const rankSec = document.getElementById('rankingSection');

  const lobbyBtn = document.getElementById('tabLobbyBtn');
  const feedBtn = document.getElementById('tabFeedBtn');
  const rankBtn = document.getElementById('tabRankBtn');

  const activeClass = "px-3 py-1.5 text-xs font-bold rounded-xl bg-white text-slate-900 shadow-sm transition-all";
  const inactiveClass = "px-3 py-1.5 text-xs font-semibold rounded-xl text-slate-500 hover:text-slate-900 transition-all";

  lobbySec.classList.add('hidden');
  feedSec.classList.add('hidden');
  rankSec.classList.add('hidden');

  lobbyBtn.className = inactiveClass;
  feedBtn.className = inactiveClass;
  rankBtn.className = inactiveClass;

  if (tab === 'feed') {
    feedSec.classList.remove('hidden');
    feedBtn.className = activeClass;
    renderPosts();
  } else if (tab === 'ranking') {
    rankSec.classList.remove('hidden');
    rankBtn.className = activeClass;
    renderRanking();
  } else {
    lobbySec.classList.remove('hidden');
    lobbyBtn.className = activeClass;
    renderLobby();
  }

  window.scrollTo({ top: 0, behavior: 'smooth' });
}

function goToCreatePost() {
  navigateTo('feed');
  setFeedFilter('all');
  setTimeout(() => {
    document.getElementById('createPostCard').scrollIntoView({ behavior: 'smooth' });
    document.getElementById('postTitle').focus();
  }, 100);
}

function goToSolvePost() {
  navigateTo('feed');
  setFeedFilter('unsolved');
}

// 개발자 모드 인증 (비밀번호: joon12191219)
function toggleDevMode() {
  if (isDeveloperMode) {
    if (confirm('개발자 모드를 해제하시겠습니까?')) {
      isDeveloperMode = false;
      localStorage.removeItem('gomin_is_dev');
      alert('개발자 권한이 해제되었습니다.');
      updateHeaderUI();
      renderPosts();
    }
  } else {
    document.getElementById('devAuthModal').classList.remove('hidden');
    document.getElementById('devPasswordInput').focus();
  }
}

function closeDevModal() {
  document.getElementById('devAuthModal').classList.add('hidden');
  document.getElementById('devPasswordInput').value = '';
}

function handleDevLogin(e) {
  e.preventDefault();
  const passwordInput = document.getElementById('devPasswordInput').value;
  if (passwordInput === 'joon12191219') {
    isDeveloperMode = true;
    localStorage.setItem('gomin_is_dev', 'true');
    alert('⚡ 개발자 인증 성공! 모든 게시글과 댓글 관리 권한을 얻었습니다.');
    closeDevModal();
    updateHeaderUI();
    renderPosts();
  } else {
    alert('비밀번호가 올바르지 않습니다.');
  }
}

// 삭제 처리
function handleDeletePost(postId) {
  if (!confirm('정말 이 고민을 삭제하시겠습니까?')) return;
  let posts = getPosts();
  posts = posts.filter(p => p.id !== postId);
  setPosts(posts);
  renderPosts();
  renderLobby();
}

function handleDeleteComment(postId, commentId) {
  if (!confirm('정말 이 답변을 삭제하시겠습니까?')) return;
  const posts = getPosts();
  const post = posts.find(p => p.id === postId);
  if (post) {
    post.comments = post.comments.filter(c => c.id !== commentId);
    setPosts(posts);
    renderPosts();
  }
}

// 공감하기 버튼
function handleToggleLike(postId) {
  const currentUser = getCurrentUser();
  const posts = getPosts();
  const post = posts.find(p => p.id === postId);

  if (post) {
    if (!post.likes) post.likes = [];
    const index = post.likes.indexOf(currentUser);
    if (index > -1) {
      post.likes.splice(index, 1);
    } else {
      post.likes.push(currentUser);
    }
    setPosts(posts);
    renderPosts();
  }
}

// 프로필 모달 관련
function openProfileModal(isEdit = false) {
  const modal = document.getElementById('profileModal');
  const closeBtn = document.getElementById('closeProfileBtn');
  const modalTitle = document.getElementById('modalTitle');
  const nicknameInput = document.getElementById('nicknameInput');
  
  modal.classList.remove('hidden');

  if (isEdit) {
    closeBtn.classList.remove('hidden');
    modalTitle.textContent = "✏️ 프로필 수정";
    const currentUser = getCurrentUser();
    const users = getUsers();
    const userData = users[currentUser] || { avatar: '🦊' };
    
    nicknameInput.value = currentUser;
    selectedAvatarData = userData.avatar || '🦊';
  } else {
    closeBtn.classList.add('hidden');
    modalTitle.textContent = "🎯 프로필 설정";
    nicknameInput.value = '';
    selectedAvatarData = '🦊';
  }

  renderAvatarPreview(selectedAvatarData);
}

function closeProfileModal() {
  document.getElementById('profileModal').classList.add('hidden');
}

function selectPresetAvatar(emoji) {
  selectedAvatarData = emoji;
  renderAvatarPreview(emoji);
}

function handleFileUpload(event) {
  const file = event.target.files[0];
  if (file) {
    const reader = new FileReader();
    reader.onload = function(e) {
      selectedAvatarData = e.target.result;
      renderAvatarPreview(selectedAvatarData);
    };
    reader.readAsDataURL(file);
  }
}

function renderAvatarPreview(avatarData) {
  const preview = document.getElementById('avatarPreview');
  if (avatarData.startsWith('data:image')) {
    preview.innerHTML = `<img src="${avatarData}" class="w-full h-full object-cover">`;
  } else {
    preview.innerHTML = avatarData;
  }
}

function renderAvatarHTML(avatarData, sizeClasses = "w-7 h-7 text-xs") {
  if (avatarData && avatarData.startsWith('data:image')) {
    return `<div class="${sizeClasses} rounded-xl overflow-hidden border border-slate-200/80 shrink-0"><img src="${avatarData}" class="w-full h-full object-cover"></div>`;
  }
  return `<div class="${sizeClasses} rounded-xl bg-teal-50 flex items-center justify-center border border-teal-200/60 shrink-0">${avatarData || '🦊'}</div>`;
}

function handleSaveProfile(e) {
  e.preventDefault();
  const newNickname = document.getElementById('nicknameInput').value.trim();
  if (!newNickname) return;

  const oldNickname = getCurrentUser();
  const users = getUsers();

  let currentPoints = 0;
  let userNotifs = [];
  if (oldNickname && users[oldNickname]) {
    currentPoints = users[oldNickname].points || 0;
    userNotifs = users[oldNickname].notifications || [];
    if (oldNickname !== newNickname) {
      delete users[oldNickname];
    }
  }

  users[newNickname] = {
    points: currentPoints,
    avatar: selectedAvatarData,
    notifications: userNotifs
  };

  setUsers(users);
  setCurrentUser(newNickname);

  closeProfileModal();
  updateHeaderUI();
  renderLobby();
  renderPosts();
}

function updateHeaderUI() {
  const currentUser = getCurrentUser();
  const users = getUsers();
  const userData = users[currentUser] || { points: 0, avatar: '🦊', notifications: [] };

  document.getElementById('headerNickname').textContent = currentUser;
  document.getElementById('headerPoints').textContent = `${userData.points || 0} pt`;
  
  const avatarContainer = document.getElementById('headerAvatar');
  if (userData.avatar && userData.avatar.startsWith('data:image')) {
    avatarContainer.innerHTML = `<img src="${userData.avatar}" class="w-full h-full object-cover">`;
  } else {
    avatarContainer.innerHTML = userData.avatar || '🦊';
  }

  const devBtn = document.getElementById('devAuthBtn');
  if (isDeveloperMode) {
    devBtn.className = "px-2.5 py-1.5 rounded-2xl text-xs font-black transition-all border border-purple-300 bg-purple-600 text-white shadow-sm";
    devBtn.textContent = "💻 개발자 ON";
  } else {
    devBtn.className = "px-2.5 py-1.5 rounded-2xl text-xs font-extrabold transition-all border border-slate-200/80 bg-white text-slate-600 hover:bg-slate-50";
    devBtn.textContent = "💻 개발자";
  }

  const notifs = userData.notifications || [];
  const unreadCount = notifs.filter(n => !n.read || (!n.claimed && n.type === 'ADOPTED')).length;
  const badge = document.getElementById('notifBadge');
  if (unreadCount > 0) {
    badge.textContent = unreadCount > 9 ? '9+' : unreadCount;
    badge.classList.remove('hidden');
  } else {
    badge.classList.add('hidden');
  }
}

function setFeedFilter(filter) {
  currentFeedFilter = filter;
  const btns = {
    all: document.getElementById('filterAllBtn'),
    unsolved: document.getElementById('filterUnsolvedBtn'),
    myPosts: document.getElementById('filterMyPostsBtn'),
    myComments: document.getElementById('filterMyCommentsBtn')
  };

  Object.keys(btns).forEach(key => {
    if (btns[key]) {
      btns[key].className = "px-3 py-1 rounded-xl text-slate-600 font-medium hover:text-slate-900";
    }
  });

  if (btns[filter]) {
    btns[filter].className = "px-3 py-1 rounded-xl bg-white font-bold text-slate-900 shadow-sm";
  }

  renderPosts();
}

// 고민 작성
function handleCreatePost(e) {
  e.preventDefault();
  const titleInput = document.getElementById('postTitle');
  const contentInput = document.getElementById('postContent');
  const categorySelect = document.getElementById('postCategory');
  const currentUser = getCurrentUser();

  const newPost = {
    id: Date.now(),
    author: currentUser,
    category: categorySelect.value || '기타',
    title: titleInput.value.trim(),
    content: contentInput.value.trim(),
    createdAt: new Date().toLocaleDateString('ko-KR', { month: 'short', day: 'numeric', hour: '2-digit', minute: '2-digit' }),
    isAdopted: false,
    likes: [],
    comments: []
  };

  const posts = getPosts();
  posts.unshift(newPost);
  setPosts(posts);

  titleInput.value = '';
  contentInput.value = '';
  renderPosts();
  renderLobby();
}

// 답변 작성
function handleAddComment(postId) {
  const input = document.getElementById(`commentInput-${postId}`);
  const content = input.value.trim();
  if (!content) return;

  const currentUser = getCurrentUser();
  const posts = getPosts();
  const post = posts.find(p => p.id === postId);

  if (post) {
    post.comments.push({
      id: Date.now(),
      author: currentUser,
      content: content,
      isAdopted: false
    });
    setPosts(posts);

    if (post.author !== currentUser) {
      sendNotification(post.author, {
        id: Date.now(),
        type: 'COMMENT',
        title: '💬 내 고민에 새로운 솔루션 도착!',
        message: `'${post.title}' 고민에 ${currentUser}님이 조언을 남겼습니다.`,
        createdAt: new Date().toLocaleDateString('ko-KR', { month: 'short', day: 'numeric', hour: '2-digit', minute: '2-digit' }),
        read: false,
        claimed: true
      });
    }

    renderPosts();
  }
}

// 답변 채택 (본인 답변 채택 방지)
function handleAdoptComment(postId, commentId, commentAuthor) {
  const currentUser = getCurrentUser();
  const posts = getPosts();
  const post = posts.find(p => p.id === postId);

  if (!post || post.author !== currentUser || post.isAdopted) return;

  if (commentAuthor === post.author) {
    alert('자신의 고민에 직접 적은 답변은 채택할 수 없습니다.');
    return;
  }

  post.isAdopted = true;
  const comment = post.comments.find(c => c.id === commentId);
  if (comment) comment.isAdopted = true;

  setPosts(posts);

  sendNotification(commentAuthor, {
    id: Date.now(),
    type: 'ADOPTED',
    title: '🎉 최고의 솔루션으로 Pick 되었습니다!',
    message: `'${post.title}' 고민에서 작성자님이 내 답변을 최고의 솔루션으로 선택하셨습니다!`,
    createdAt: new Date().toLocaleDateString('ko-KR', { month: 'short', day: 'numeric', hour: '2-digit', minute: '2-digit' }),
    read: false,
    claimed: false,
    points: 100
  });

  renderPosts();
  renderLobby();
  alert('최고의 의견으로 채택하셨습니다! 상대방에게 포인트 수령 알림이 전달되었습니다.');
}

function sendNotification(targetUser, notifObj) {
  const users = getUsers();
  if (!users[targetUser]) return;

  if (!users[targetUser].notifications) {
    users[targetUser].notifications = [];
  }
  users[targetUser].notifications.unshift(notifObj);
  setUsers(users);
  updateHeaderUI();
}

function toggleNotificationModal() {
  const modal = document.getElementById('notificationModal');
  const isHidden = modal.classList.contains('hidden');

  if (isHidden) {
    modal.classList.remove('hidden');
    renderNotifications();
    markAllNotificationsRead();
  } else {
    modal.classList.add('hidden');
  }
}

function markAllNotificationsRead() {
  const currentUser = getCurrentUser();
  const users = getUsers();
  if (users[currentUser] && users[currentUser].notifications) {
    users[currentUser].notifications.forEach(n => n.read = true);
    setUsers(users);
    updateHeaderUI();
  }
}

function renderNotifications() {
  const currentUser = getCurrentUser();
  const users = getUsers();
  const notifsContainer = document.getElementById('notificationList');
  const bannerContainer = document.getElementById('batchClaimBanner');
  const userNotifs = (users[currentUser] && users[currentUser].notifications) || [];

  const unclaimedNotifs = userNotifs.filter(n => n.type === 'ADOPTED' && !n.claimed);
  const totalUnclaimedPoints = unclaimedNotifs.reduce((sum, n) => sum + (n.points || 100), 0);

  if (totalUnclaimedPoints > 0) {
    bannerContainer.innerHTML = `
      <div class="bg-gradient-to-r from-teal-600 to-emerald-600 text-white p-4 rounded-2xl shadow-md flex items-center justify-between">
        <div class="space-y-0.5">
          <span class="text-[10px] font-extrabold text-teal-200 tracking-wider block">🎁 수령 대기 중인 혜택</span>
          <strong class="text-sm font-black">${totalUnclaimedPoints} pt 누적됨 (${unclaimedNotifs.length}건)</strong>
        </div>
        <button onclick="handleClaimAllPoints()" class="bg-white hover:bg-teal-50 text-teal-800 font-extrabold px-3.5 py-2 rounded-xl text-xs transition-all shadow-sm active:scale-95">
          한번에 모두 수령하기
        </button>
      </div>
    `;
  } else {
    bannerContainer.innerHTML = '';
  }

  if (userNotifs.length === 0) {
    notifsContainer.innerHTML = `<p class="text-center text-slate-400 text-xs py-10">도착한 알림이 없습니다.</p>`;
    return;
  }

  notifsContainer.innerHTML = userNotifs.map(n => {
    const isAdoptType = n.type === 'ADOPTED';

    return `
      <div class="p-4 rounded-2xl border ${!n.claimed && isAdoptType ? 'bg-teal-50/60 border-teal-200/80' : 'bg-slate-50/70 border-slate-100'} text-xs space-y-2">
        <div class="flex items-center justify-between">
          <span class="font-bold text-slate-900 flex items-center gap-1.5">${escapeHtml(n.title)}</span>
          <span class="text-[10px] text-slate-400">${n.createdAt}</span>
        </div>
        <p class="text-slate-600 leading-relaxed">${escapeHtml(n.message)}</p>
        
        ${isAdoptType ? `
          <div class="pt-1 flex justify-end">
            ${!n.claimed ? `
              <button onclick="handleClaimSinglePoint(${n.id})" class="bg-teal-600 hover:bg-teal-700 text-white font-bold px-3 py-1.5 rounded-xl text-xs transition-all shadow-sm flex items-center gap-1 active:scale-95">
                🎁 ${n.points || 100}pt 수령하기
              </button>
            ` : `
              <span class="text-[11px] font-bold text-teal-600 bg-teal-100/70 border border-teal-200 px-2.5 py-1 rounded-lg">
                ✓ 수령 완료
              </span>
            `}
          </div>
        ` : ''}
      </div>
    `;
  }).join('');
}

function handleClaimSinglePoint(notifId) {
  const currentUser = getCurrentUser();
  const users = getUsers();
  const user = users[currentUser];

  if (!user || !user.notifications) return;

  const notif = user.notifications.find(n => n.id === notifId);
  if (notif && !notif.claimed) {
    notif.claimed = true;
    const pts = notif.points || 100;
    user.points = (user.points || 0) + pts;
    setUsers(users);

    showClaimSuccessModal(pts);
  }
}

function handleClaimAllPoints() {
  const currentUser = getCurrentUser();
  const users = getUsers();
  const user = users[currentUser];

  if (!user || !user.notifications) return;

  const unclaimedNotifs = user.notifications.filter(n => n.type === 'ADOPTED' && !n.claimed);
  if (unclaimedNotifs.length === 0) return;

  let totalAddPoints = 0;
  unclaimedNotifs.forEach(n => {
    n.claimed = true;
    totalAddPoints += (n.points || 100);
  });

  user.points = (user.points || 0) + totalAddPoints;
  setUsers(users);

  showClaimSuccessModal(totalAddPoints);
}

function showClaimSuccessModal(points) {
  jsConfetti.addConfetti({
    emojis: ['🎉', '✨', '🎁', '💎', '💡'],
    confettiNumber: 120,
  });

  document.getElementById('claimedPointsText').textContent = `${points} 포인트`;
  document.getElementById('claimSuccessModal').classList.remove('hidden');
  renderNotifications();
  updateHeaderUI();
}

function closeClaimModal() {
  document.getElementById('claimSuccessModal').classList.add('hidden');
}

function renderLobby() {
  const container = document.getElementById('lobbyPostsPreview');
  const posts = getPosts();

  const totalPosts = posts.length;
  const solvedPosts = posts.filter(p => p.isAdopted).length;
  const solveRate = totalPosts > 0 ? Math.round((solvedPosts / totalPosts) * 100) : 0;

  document.getElementById('statTotalPosts').textContent = totalPosts;
  document.getElementById('statSolvedPosts').textContent = solvedPosts;
  document.getElementById('statSolveRate').textContent = `${solveRate}%`;

  const previewPosts = posts.slice(0, 3);

  if (previewPosts.length === 0) {
    container.innerHTML = `<p class="text-slate-400 text-xs py-3 text-center">등록된 고민이 아직 없습니다.</p>`;
    return;
  }

  container.innerHTML = previewPosts.map(post => `
    <div onclick="navigateTo('feed')" class="p-3.5 bg-slate-50/70 hover:bg-teal-50/40 rounded-2xl cursor-pointer transition-all border border-slate-100 flex items-center justify-between">
      <div class="space-y-0.5">
        <div class="flex items-center space-x-1.5">
          <span class="text-[9px] font-bold px-1.5 py-0.5 rounded bg-slate-200 text-slate-600">${escapeHtml(post.category || '기타')}</span>
          <h4 class="text-xs font-bold text-slate-800 line-clamp-1">${escapeHtml(post.title)}</h4>
        </div>
        <p class="text-[11px] text-slate-500 line-clamp-1">${escapeHtml(post.content)}</p>
      </div>
      <span class="text-[10px] font-bold px-2.5 py-1 rounded-full ${post.isAdopted ? 'bg-teal-100 text-teal-700' : 'bg-amber-100 text-amber-700'} shrink-0 ml-3">
        ${post.isAdopted ? 'Pick 완료' : '답변 대기'}
      </span>
    </div>
  `).join('');
}

// Render Posts Feed
function renderPosts() {
  const postsContainer = document.getElementById('postsList');
  let posts = getPosts();
  const users = getUsers();
  const currentUser = getCurrentUser();

  const searchKeyword = (document.getElementById('searchInput')?.value || '').toLowerCase().trim();
  if (searchKeyword) {
    posts = posts.filter(p => p.title.toLowerCase().includes(searchKeyword) || p.content.toLowerCase().includes(searchKeyword));
  }

  const catFilter = document.getElementById('categoryFilter')?.value || 'ALL';
  if (catFilter !== 'ALL') {
    posts = posts.filter(p => p.category === catFilter);
  }

  if (currentFeedFilter === 'unsolved') {
    posts = posts.filter(p => !p.isAdopted);
  } else if (currentFeedFilter === 'myPosts') {
    posts = posts.filter(p => p.author === currentUser);
  } else if (currentFeedFilter === 'myComments') {
    posts = posts.filter(p => p.comments.some(c => c.author === currentUser));
  }

  if (posts.length === 0) {
    postsContainer.innerHTML = `
      <div class="text-center py-12 bg-white rounded-3xl border border-slate-200/80 shadow-sm">
        <p class="text-slate-400 text-xs">조건에 맞는 고민이 존재하지 않습니다.<br>새로운 고민을 공유해 보세요!</p>
      </div>`;
    return;
  }

  postsContainer.innerHTML = posts.map(post => {
    const isPostAuthor = post.author === currentUser;
    const canDeletePost = isPostAuthor || isDeveloperMode;
    const postAuthorData = users[post.author] || { avatar: '🦊' };
    
    const likesList = post.likes || [];
    const isLiked = likesList.includes(currentUser);

    const commentsHTML = post.comments.map(comment => {
      const isCommentAuthorPostAuthor = comment.author === post.author;
      const isCommentAuthor = comment.author === currentUser;
      const canDeleteComment = isCommentAuthor || isDeveloperMode;
      const showAdoptBtn = isPostAuthor && !post.isAdopted && !isCommentAuthorPostAuthor;
      const commentAuthorData = users[comment.author] || { avatar: '🦊' };

      return `
        <div class="p-3.5 rounded-2xl ${comment.isAdopted ? 'bg-teal-50/80 border border-teal-200/80' : 'bg-slate-50/80'} text-xs space-y-1.5">
          <div class="flex items-center justify-between">
            <div class="flex items-center space-x-2">
              ${renderAvatarHTML(commentAuthorData.avatar, "w-5 h-5 text-[10px]")}
              <span class="font-bold text-slate-800">${escapeHtml(comment.author)}</span>
              ${isCommentAuthorPostAuthor ? '<span class="text-[9px] text-teal-600 font-extrabold bg-teal-100 px-1.5 py-0.2 rounded-md">작성자</span>' : ''}
            </div>
            
            <div class="flex items-center space-x-2">
              ${comment.isAdopted ? '<span class="text-teal-600 font-black text-[11px] flex items-center gap-1">✨ Pick 채택됨</span>' : ''}
              
              ${canDeleteComment ? `
                <button onclick="handleDeleteComment(${post.id}, ${comment.id})" class="text-[10px] text-rose-500 hover:text-rose-700 hover:underline font-semibold ml-2">
                  삭제
                </button>
              ` : ''}
            </div>
          </div>
          <p class="text-slate-600 leading-relaxed pl-7">${escapeHtml(comment.content)}</p>
          ${showAdoptBtn ? `
            <div class="pt-1 flex justify-end">
              <button onclick="handleAdoptComment(${post.id}, ${comment.id}, '${escapeHtml(comment.author)}')"
                class="bg-teal-600 hover:bg-teal-700 text-white font-bold px-3 py-1.5 rounded-xl text-[11px] transition-all shadow-sm active:scale-95">
                최고의 의견으로 Pick (+100pt)
              </button>
            </div>
          ` : ''}
        </div>
      `;
    }).join('');

    return `
      <div class="bg-white rounded-3xl p-6 shadow-sm border border-slate-200/80 space-y-4">
        <div class="flex items-start justify-between">
          <div class="flex items-center space-x-3">
            ${renderAvatarHTML(postAuthorData.avatar, "w-10 h-10 text-base")}
            <div>
              <div class="flex items-center space-x-2">
                <span class="text-[10px] font-extrabold px-2 py-0.5 rounded-md bg-slate-100 text-slate-600 border border-slate-200">${escapeHtml(post.category || '기타')}</span>
                <span class="text-xs font-bold text-slate-800">${escapeHtml(post.author)}</span>
                <span class="text-xs text-slate-300">•</span>
                <span class="text-[10px] text-slate-400">${post.createdAt}</span>
                
                ${canDeletePost ? `
                  <button onclick="handleDeletePost(${post.id})" class="text-[11px] font-bold text-rose-500 hover:text-rose-700 hover:underline ml-1">
                    삭제
                  </button>
                ` : ''}
              </div>
              <h3 class="text-base font-bold text-slate-900 mt-1 tracking-tight">${escapeHtml(post.title)}</h3>
            </div>
          </div>
          ${post.isAdopted ? `
            <span class="px-3 py-1 rounded-full text-xs font-extrabold bg-teal-50 text-teal-700 border border-teal-200/60">Pick 완료</span>
          ` : `
            <span class="px-3 py-1 rounded-full text-xs font-semibold bg-amber-50 text-amber-700 border border-amber-200/60">답변 대기</span>
          `}
        </div>

        <p class="text-xs sm:text-sm text-slate-600 leading-relaxed whitespace-pre-line pl-1">${escapeHtml(post.content)}</p>

        <div class="flex items-center justify-between pt-1">
          <button onclick="handleToggleLike(${post.id})" class="flex items-center gap-1.5 px-3 py-1.5 rounded-xl border text-xs font-bold transition-all ${isLiked ? 'bg-rose-50 border-rose-200 text-rose-600' : 'bg-slate-50 border-slate-200/80 text-slate-500 hover:bg-slate-100'}">
            <span>${isLiked ? '❤️' : '🤍'}</span>
            <span>공감해요 ${likesList.length > 0 ? likesList.length : ''}</span>
          </button>
        </div>

        <div class="border-t border-slate-100 pt-4 space-y-3">
          <div class="space-y-2">
            ${commentsHTML}
          </div>

          <div class="flex space-x-2 pt-1">
            <input type="text" id="commentInput-${post.id}" placeholder="조언이나 따뜻한 솔루션을 남겨주세요."
              class="flex-1 px-4 py-2.5 rounded-2xl border border-slate-200 focus:outline-none focus:ring-2 focus:ring-teal-500/50 text-xs bg-slate-50/50 focus:bg-white transition-all">
            <button onclick="handleAddComment(${post.id})" class="bg-slate-900 hover:bg-slate-800 text-white px-4 py-2.5 rounded-2xl text-xs font-bold transition-all shadow-sm active:scale-95">
              답변 달기
            </button>
          </div>
        </div>
      </div>
    `;
  }).join('');
}

function renderRanking() {
  const users = getUsers();
  const rankingContainer = document.getElementById('rankingList');
  
  const sortedUsers = Object.entries(users)
    .map(([nickname, data]) => ({ nickname, points: data.points || 0, avatar: data.avatar || '🦊' }))
    .sort((a, b) => b.points - a.points);

  if (sortedUsers.length === 0) {
    rankingContainer.innerHTML = `<p class="text-center text-slate-400 text-xs py-6">유저 데이터가 존재하지 않습니다.</p>`;
    return;
  }

  rankingContainer.innerHTML = sortedUsers.map((u, idx) => {
    let rankBadge = `<span class="w-6 text-center text-xs font-bold text-slate-400">${idx + 1}</span>`;
    if (idx === 0) rankBadge = `<span class="w-6 text-center text-base">🥇</span>`;
    if (idx === 1) rankBadge = `<span class="w-6 text-center text-base">🥈</span>`;
    if (idx === 2) rankBadge = `<span class="w-6 text-center text-base">🥉</span>`;

    return `
      <div class="flex items-center justify-between p-3.5 rounded-2xl ${idx < 3 ? 'bg-slate-50/80 border border-slate-100' : ''}">
        <div class="flex items-center space-x-3">
          ${rankBadge}
          ${renderAvatarHTML(u.avatar, "w-8 h-8 text-sm")}
          <span class="text-xs font-bold text-slate-800">${escapeHtml(u.nickname)}</span>
        </div>
        <span class="text-xs font-black text-teal-600">${u.points} pt</span>
      </div>
    `;
  }).join('');
}

function escapeHtml(str) {
  return str.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;").replace(/"/g, "&quot;").replace(/'/g, "&#039;");
}

window.onload = initApp;