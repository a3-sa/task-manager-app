import React, { useState, useEffect, useMemo } from 'react';

// --- أيقونات SVG ---
const MyTasksIcon = () => <svg xmlns="http://www.w3.org/2000/svg" className="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-3 7h3m-3 4h3m-6-4h.01M9 16h.01" /></svg>;
const TeamTasksIcon = () => <svg xmlns="http://www.w3.org/2000/svg" className="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.653-.084-1.28-.24-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.653.084-1.28.24-1.857m10.514-10.843A3.003 3.003 0 0012 6c-1.657 0-3 1.343-3 3s1.343 3 3 3a3.003 3.003 0 002.514-1.313M7 9a3 3 0 116 0 3 3 0 01-6 0z" /></svg>;
const LeaveIcon = () => <svg xmlns="http://www.w3.org/2000/svg" className="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M15 17h5l-1.405-1.405A2.032 2.032 0 0118 14.158V11a6.002 6.002 0 00-4-5.659V5a2 2 0 10-4 0v.341C7.67 6.165 6 8.388 6 11v3.159c0 .538-.214 1.055-.595 1.436L4 17h5m6 0v1a3 3 0 11-6 0v-1m6 0H9" /></svg>;
const CalendarIcon = () => <svg xmlns="http://www.w3.org/2000/svg" className="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z" /></svg>;
const AdminIcon = () => <svg xmlns="http://www.w3.org/2000/svg" className="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z" /><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" /></svg>;
const EditIcon = () => <svg xmlns="http://www.w3.org/2000/svg" className="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z" /></svg>;
const DeleteIcon = () => <svg xmlns="http://www.w3.org/2000/svg" className="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" /></svg>;


// --- قاعدة بيانات محلية (للتجربة المضمونة) ---
let MOCK_USERS_DB = {
    'a3': { id: 'manager-a3', name: 'المدير A3', username: 'A3', role: 'manager', password: '123456' },
    'saleh': { id: 'employee-saleh', name: 'صالح الأحمد', username: 'saleh', role: 'employee', password: 'saleh123' },
    'fahad': { id: 'employee-fahad', name: 'فهد عبدالله', username: 'fahad', role: 'employee', password: 'fahad123' }
};

let MOCK_TASKS_DB = [
    { id: 1, title: 'تصميم الصفحة الرئيسية للموقع', description: 'يجب أن يكون التصميم عصرياً ومتوافقاً مع الهوية البصرية للشركة.', projectName: 'تطوير الموقع', assignedTo: 'employee-saleh', startDate: '2025-07-20', endDate: '2025-07-25', status: 'in-progress' },
    { id: 2, title: 'إعداد خطة المحتوى للشهر القادم', description: 'التركيز على منشورات تفاعلية لزيادة المشاركة على منصات التواصل الاجتماعي.', projectName: 'التسويق الرقمي', assignedTo: 'employee-fahad', startDate: '2025-07-22', endDate: '2025-07-28', status: 'not-started' },
    { id: 3, title: 'مراجعة أداء الحملة الإعلانية', description: 'تحليل النتائج وتقديم تقرير مفصل عن العائد على الاستثمار.', projectName: 'التسويق الرقمي', assignedTo: 'manager-a3', startDate: '2025-07-18', endDate: '2025-07-21', status: 'completed' },
    { id: 4, title: 'ربط الواجهة الخلفية بنظام الدفع', description: 'التأكد من أن عملية الدفع آمنة وسلسة للمستخدم.', projectName: 'تطوير الموقع', assignedTo: 'employee-saleh', startDate: '2025-07-26', endDate: '2025-08-05', status: 'not-started' },
];

let MOCK_STEPS_DB = [
    { id: 101, taskId: 1, title: 'تصميم الواجهة الأولية (wireframe)', dueDate: '2025-07-21', isCompleted: true },
    { id: 102, taskId: 1, title: 'اختيار الألوان والخطوط', dueDate: '2025-07-22', isCompleted: true },
    { id: 103, taskId: 1, title: 'تنفيذ التصميم النهائي', dueDate: '2025-07-24', isCompleted: false, notes: [{text: 'الرجاء التركيز على سهولة الاستخدام.', author: 'المدير A3'}] },
    { id: 201, taskId: 2, title: 'تحليل المنافسين', dueDate: '2025-07-23', isCompleted: false },
];

let MOCK_LEAVES_DB = [
    {id: 1, userId: 'employee-fahad', userName: 'فهد عبدالله', startDate: '2025-08-10', endDate: '2025-08-12', reason: 'ظرف طارئ', status: 'approved'},
    {id: 2, userId: 'employee-noura', userName: 'نورة محمد', startDate: '2025-08-10', endDate: '2025-08-15', reason: 'إجازة سنوية', status: 'approved'}
];

// --- المكون الرئيسي ---
function App() {
    const [userData, setUserData] = useState(null);
    const [loading, setLoading] = useState(false);

    const handleLogin = (username, password) => {
        setLoading(true);
        const userToLogin = MOCK_USERS_DB[username.toLowerCase().trim()];
        if (userToLogin && userToLogin.password === password) {
            setUserData(userToLogin);
            setLoading(false);
            return null;
        } else {
            setLoading(false);
            return "فشل الدخول. تحقق من اسم المستخدم وكلمة المرور.";
        }
    };

    const handleLogout = () => setUserData(null);

    if (loading) return <div className="flex justify-center items-center h-screen bg-gray-100 text-xl">جاري الدخول...</div>;

    return (
        <div dir="rtl" className="bg-gray-100 min-h-screen font-sans text-gray-800">
            {userData ? <Layout userData={userData} onLogout={handleLogout} /> : <Login onLogin={handleLogin} />}
        </div>
    );
}

function Login({ onLogin }) {
    const [username, setUsername] = useState('');
    const [password, setPassword] = useState('');
    const [error, setError] = useState('');
    const [loading, setLoading] = useState(false);

    const handleSubmit = (e) => {
        e.preventDefault();
        setLoading(true);
        const loginError = onLogin(username, password);
        if (loginError) {
            setError(loginError);
            setLoading(false);
        }
    };

    return (
        <div className="flex items-center justify-center min-h-screen">
            <div className="w-full max-w-md p-8 space-y-6 bg-white rounded-xl shadow-lg">
                <h1 className="text-3xl font-bold text-center">منصة العمل المتكاملة</h1>
                <form onSubmit={handleSubmit} className="space-y-6">
                    <div><label className="block text-sm font-medium">اسم المستخدم</label><input type="text" value={username} onChange={(e) => setUsername(e.target.value)} className="w-full px-3 py-2 mt-1 border rounded-md" required /></div>
                    <div><label className="block text-sm font-medium">كلمة المرور</label><input type="password" value={password} onChange={(e) => setPassword(e.target.value)} className="w-full px-3 py-2 mt-1 border rounded-md" required /></div>
                    {error && <p className="text-sm text-red-600">{error}</p>}
                    <button type="submit" disabled={loading} className="w-full px-4 py-2 font-bold text-white bg-indigo-600 rounded-md hover:bg-indigo-700 disabled:bg-indigo-400">{loading ? 'جاري الدخول...' : 'دخول'}</button>
                </form>
            </div>
        </div>
    );
}

function Layout({ userData, onLogout }) {
    const [activeView, setActiveView] = useState(userData.role === 'manager' ? 'team-tasks-dashboard' : 'my-tasks');
    const [allUsers, setAllUsers] = useState(Object.values(MOCK_USERS_DB));
    const [tasks, setTasks] = useState(MOCK_TASKS_DB);

    const NavButton = ({ view, icon, label }) => (
        <button onClick={() => setActiveView(view)} className={`w-full flex flex-col items-center p-3 my-1 rounded-lg transition-colors duration-200 ${activeView === view ? 'bg-indigo-600 text-white' : 'text-gray-300 hover:bg-gray-700'}`}>
            {icon}
            <span className="text-xs mt-1 font-semibold">{label}</span>
        </button>
    );

    return (
        <div className="flex h-screen">
            <aside className="w-24 bg-gray-800 text-white p-2 flex flex-col items-center flex-shrink-0">
                <img src="https://pbs.twimg.com/profile_images/1495321590538043393/B_V22x-l_400x400.jpg" alt="شعار إنفاذ" className="w-16 h-auto rounded-md mb-8" />
                {userData.role === 'manager' ? (
                    <NavButton view="team-tasks-dashboard" icon={<TeamTasksIcon />} label="متابعة الفريق" />
                ) : (
                    <NavButton view="my-tasks" icon={<MyTasksIcon />} label="مهامي" />
                )}
                <NavButton view="leave-requests" icon={<LeaveIcon />} label="طلبات الإجازة" />
                <NavButton view="team-leaves-calendar" icon={<CalendarIcon />} label="جدول الإجازات" />
                {userData.role === 'manager' && <NavButton view="admin" icon={<AdminIcon />} label="الإدارة" />}
                <div className="mt-auto w-full"><button onClick={onLogout} className="w-full text-xs text-gray-400 hover:text-white p-2">خروج</button></div>
            </aside>
            <main className="flex-1 p-6 md:p-8 overflow-y-auto">
                {activeView === 'my-tasks' && <EmployeeTasksView currentUser={userData} allUsers={allUsers} tasks={tasks} />}
                {activeView === 'team-tasks-dashboard' && <ManagerTasksDashboard allUsers={allUsers} currentUser={userData} tasks={tasks} />}
                {activeView === 'leave-requests' && <LeaveRequestsView currentUser={userData} />}
                {activeView === 'team-leaves-calendar' && <TeamLeavesCalendarView />}
                {activeView === 'admin' && userData.role === 'manager' && <AdminPanel allUsers={allUsers} setAllUsers={setAllUsers} setTasks={setTasks} />}
            </main>
        </div>
    );
}

// --- المكونات التفصيلية لكل قسم ---

const TaskCard = ({ task, allUsers, onTaskClick }) => {
    const assignedUser = allUsers.find(u => u.id === task.assignedTo);
    const steps = MOCK_STEPS_DB.filter(s => s.taskId === task.id);
    const completedSteps = steps.filter(s => s.isCompleted).length;
    const progress = steps.length > 0 ? Math.round((completedSteps / steps.length) * 100) : 0;

    return (
        <div onClick={() => onTaskClick(task)} className="bg-white p-4 rounded-lg shadow-sm border border-gray-200 space-y-3 cursor-pointer hover:shadow-md transition-shadow">
            <h3 className="font-bold text-gray-800">{task.title}</h3>
            <p className="text-sm text-gray-500 bg-gray-50 p-2 rounded-md">{task.projectName}</p>
            <div>
                <div className="flex justify-between items-center mb-1"><span className="text-xs font-semibold text-gray-600">التقدم</span><span className="text-xs font-semibold text-indigo-600">{progress}%</span></div>
                <div className="w-full bg-gray-200 rounded-full h-2.5"><div className="bg-indigo-600 h-2.5 rounded-full" style={{ width: `${progress}%` }}></div></div>
            </div>
            <div className="flex justify-between items-center text-sm text-gray-600 border-t pt-2"><span>{assignedUser ? `👤 ${assignedUser.name}` : ''}</span><span className="text-xs">🗓️ {task.endDate}</span></div>
        </div>
    );
};

function EmployeeTasksView({ currentUser, allUsers, tasks }) {
    const [selectedTask, setSelectedTask] = useState(null);
    const myTasks = tasks.filter(t => t.assignedTo === currentUser.id);
    const columns = {
        'not-started': myTasks.filter(t => t.status === 'not-started'),
        'in-progress': myTasks.filter(t => t.status === 'in-progress'),
        'completed': myTasks.filter(t => t.status === 'completed'),
    };

    return (
        <div>
            {selectedTask && <TaskDetailModal task={selectedTask} onClose={() => setSelectedTask(null)} currentUser={currentUser} />}
            <h1 className="text-3xl font-bold mb-6">مهامي</h1>
            <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
                <div className="bg-gray-100 p-4 rounded-lg">
                    <h2 className="font-bold mb-4 text-gray-600">لم تبدأ ({columns['not-started'].length})</h2>
                    <div className="space-y-4">{columns['not-started'].map(task => <TaskCard key={task.id} task={task} allUsers={allUsers} onTaskClick={setSelectedTask} />)}</div>
                </div>
                <div className="bg-gray-100 p-4 rounded-lg">
                    <h2 className="font-bold mb-4 text-blue-600">قيد التنفيذ ({columns['in-progress'].length})</h2>
                    <div className="space-y-4">{columns['in-progress'].map(task => <TaskCard key={task.id} task={task} allUsers={allUsers} onTaskClick={setSelectedTask} />)}</div>
                </div>
                <div className="bg-gray-100 p-4 rounded-lg">
                    <h2 className="font-bold mb-4 text-green-600">مكتملة ({columns['completed'].length})</h2>
                    <div className="space-y-4">{columns['completed'].map(task => <TaskCard key={task.id} task={task} allUsers={allUsers} onTaskClick={setSelectedTask} />)}</div>
                </div>
            </div>
        </div>
    );
}

function ManagerTasksDashboard({ allUsers, currentUser, tasks }) {
    const [selectedTask, setSelectedTask] = useState(null);
    const employees = allUsers.filter(user => user.role === 'employee');
    const tasksByEmployee = employees.map(employee => ({
        ...employee,
        tasks: tasks.filter(task => task.assignedTo === employee.id)
    })).filter(employee => employee.tasks.length > 0);

    return (
        <div>
            {selectedTask && <TaskDetailModal task={selectedTask} onClose={() => setSelectedTask(null)} currentUser={currentUser} />}
            <h1 className="text-3xl font-bold mb-6">متابعة مهام الفريق</h1>
            <div className="space-y-8">
                {tasksByEmployee.map(employee => (
                    <div key={employee.id}>
                        <h2 className="text-xl font-bold mb-3 border-b pb-2">{employee.name} ({employee.tasks.length} مهام)</h2>
                        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
                            {employee.tasks.map(task => (<TaskCard key={task.id} task={task} allUsers={allUsers} onTaskClick={setSelectedTask} />))}
                        </div>
                    </div>
                ))}
            </div>
        </div>
    );
}

function TaskDetailModal({ task, onClose, currentUser }) {
    const [steps, setSteps] = useState(MOCK_STEPS_DB.filter(s => s.taskId === task.id));
    const [newStepTitle, setNewStepTitle] = useState('');
    const [newStepDate, setNewStepDate] = useState('');
    const [newNote, setNewNote] = useState({});

    const handleAddStep = (e) => {
        e.preventDefault();
        if(!newStepTitle || !newStepDate) return;
        const newStep = { id: Date.now(), taskId: task.id, title: newStepTitle, dueDate: newStepDate, isCompleted: false, notes: [] };
        MOCK_STEPS_DB.push(newStep);
        setSteps(prev => [...prev, newStep]);
        setNewStepTitle(''); setNewStepDate('');
    };

    const toggleStepCompletion = (stepId) => {
        const stepIndex = MOCK_STEPS_DB.findIndex(s => s.id === stepId);
        if (stepIndex > -1) {
            MOCK_STEPS_DB[stepIndex].isCompleted = !MOCK_STEPS_DB[stepIndex].isCompleted;
            setSteps([...MOCK_STEPS_DB.filter(s => s.taskId === task.id)]);
        }
    };
    
    const handleAddNote = (stepId) => {
        const noteText = newNote[stepId];
        if (!noteText || !noteText.trim()) return;
        const stepIndex = MOCK_STEPS_DB.findIndex(s => s.id === stepId);
        if (stepIndex > -1) {
            if (!MOCK_STEPS_DB[stepIndex].notes) MOCK_STEPS_DB[stepIndex].notes = [];
            MOCK_STEPS_DB[stepIndex].notes.push({ text: noteText, author: currentUser.name });
            setSteps([...MOCK_STEPS_DB.filter(s => s.taskId === task.id)]);
            setNewNote(prev => ({...prev, [stepId]: ''}));
        }
    };

    const isMyTask = task.assignedTo === currentUser.id;

    return (
        <div className="fixed inset-0 bg-black bg-opacity-60 z-50 flex justify-center items-center p-4" onClick={onClose}>
            <div className="bg-white p-6 rounded-lg shadow-xl w-full max-w-2xl max-h-[90vh] overflow-y-auto" onClick={e => e.stopPropagation()}>
                <div className="flex justify-between items-start mb-4 pb-4 border-b">
                    <div>
                        <h2 className="text-2xl font-bold">{task.title}</h2>
                        <p className="text-gray-500">{task.projectName}</p>
                        <p className="text-sm mt-2 text-gray-700">{task.description}</p>
                    </div>
                    <button onClick={onClose} className="text-gray-500 hover:text-gray-800 text-3xl">×</button>
                </div>
                {isMyTask && (
                    <form onSubmit={handleAddStep} className="mb-6 p-4 bg-gray-50 rounded-lg">
                        <h3 className="font-semibold mb-2">إضافة خطوة جديدة</h3>
                        <div className="flex flex-wrap gap-4">
                            <input type="text" placeholder="عنوان الخطوة" value={newStepTitle} onChange={e => setNewStepTitle(e.target.value)} className="flex-grow p-2 border rounded-md" required />
                            <input type="date" value={newStepDate} onChange={e => setNewStepDate(e.target.value)} max={task.endDate} className="p-2 border rounded-md" required />
                            <button type="submit" className="bg-indigo-600 text-white px-4 py-2 rounded-md hover:bg-indigo-700">إضافة</button>
                        </div>
                    </form>
                )}
                <div className="space-y-4">
                    <h3 className="font-semibold">الخطوات التنفيذية ({steps.length})</h3>
                    {steps.map(step => (
                        <div key={step.id} className="p-3 border rounded-lg">
                            <div className="flex items-center">
                                <input type="checkbox" checked={step.isCompleted} onChange={() => toggleStepCompletion(step.id)} disabled={!isMyTask} className="ml-3" />
                                <div className="flex-grow">
                                    <p className={`${step.isCompleted ? 'line-through text-gray-500' : ''}`}>{step.title}</p>
                                    <p className="text-xs text-gray-400">تاريخ الإنجاز: {step.dueDate}</p>
                                </div>
                            </div>
                            { (step.notes?.length > 0 || currentUser.role === 'manager') && (
                                <div className="mt-2 pt-2 border-t border-gray-200 ml-6">
                                    {step.notes?.map((note, index) => ( <p key={index} className="text-xs bg-yellow-100 p-1 rounded-md mb-1"><strong>{note.author}:</strong> {note.text}</p>))}
                                    {currentUser.role === 'manager' && (
                                        <div className="flex gap-2 mt-1">
                                            <input type="text" placeholder="إضافة ملاحظة..." value={newNote[step.id] || ''} onChange={e => setNewNote(prev => ({...prev, [step.id]: e.target.value}))} className="flex-grow p-1 border text-xs rounded-md" />
                                            <button onClick={() => handleAddNote(step.id)} className="text-xs bg-gray-200 px-3 rounded-md hover:bg-gray-300">إرسال</button>
                                        </div>
                                    )}
                                </div>
                            )}
                        </div>
                    ))}
                </div>
            </div>
        </div>
    );
}

function TeamLeavesCalendarView() {
    const [leaves] = useState(MOCK_LEAVES_DB.filter(l => l.status === 'approved'));
    return (
        <div>
            <h1 className="text-3xl font-bold mb-6">جدول إجازات الفريق</h1>
            <div className="bg-white p-6 rounded-lg shadow-md">
                <div className="space-y-4">
                    {leaves.length > 0 ? leaves.map(leave => (
                        <div key={leave.id} className="p-4 bg-gray-50 rounded-lg flex justify-between items-center">
                            <span className="font-semibold">{leave.userName}</span>
                            <span className="text-gray-600">من {leave.startDate} إلى {leave.endDate}</span>
                            <span className="text-sm text-gray-500">{leave.reason}</span>
                        </div>
                    )) : <p className="text-gray-500">لا توجد إجازات موافق عليها حالياً.</p>}
                </div>
            </div>
        </div>
    );
}

function LeaveRequestsView({ currentUser }) {
    const [leaves, setLeaves] = useState(MOCK_LEAVES_DB);
    const [error, setError] = useState('');
    const [showConfirm, setShowConfirm] = useState(false);
    const [requestToApprove, setRequestToApprove] = useState(null);

    const myRequests = useMemo(() => leaves.filter(r => r.userId === currentUser.id), [leaves, currentUser]);
    
    const getConcurrentLeaves = (startDate, endDate) => {
        const start = new Date(startDate);
        const end = new Date(endDate);
        let maxConcurrent = 0;
        for (let d = new Date(start); d <= end; d.setDate(d.getDate() + 1)) {
            const concurrentOnDay = MOCK_LEAVES_DB.filter(l => {
                if (l.status !== 'approved') return false;
                const leaveStart = new Date(l.startDate);
                const leaveEnd = new Date(l.endDate);
                return d >= leaveStart && d <= leaveEnd;
            }).length;
            if (concurrentOnDay > maxConcurrent) maxConcurrent = concurrentOnDay;
        }
        return maxConcurrent;
    };

    const handleRequestLeave = (e) => {
        e.preventDefault();
        setError('');
        const { startDate, endDate, reason } = e.target.elements;
        const concurrentCount = getConcurrentLeaves(startDate.value, endDate.value);
        if (concurrentCount >= 2) {
            setError("لا يمكن تقديم الطلب. اكتمل عدد الموظفين المسموح بهم بأخذ إجازة في هذا التاريخ.");
            return;
        }
        const newRequest = {id: Date.now(), userId: currentUser.id, userName: currentUser.name, startDate: startDate.value, endDate: endDate.value, reason: reason.value, status: 'pending'};
        MOCK_LEAVES_DB.push(newRequest);
        setLeaves([...MOCK_LEAVES_DB]);
        e.target.reset();
    };
    
    const handleApprovalClick = (request) => {
        const concurrentCount = getConcurrentLeaves(request.startDate, request.endDate);
        if (concurrentCount >= 2) {
            setRequestToApprove(request);
            setShowConfirm(true);
        } else {
            updateRequestStatus(request.id, 'approved');
        }
    };

    const confirmApproval = () => {
        if (requestToApprove) updateRequestStatus(requestToApprove.id, 'approved');
        setShowConfirm(false);
        setRequestToApprove(null);
    };

    const updateRequestStatus = (id, status) => {
        const leaveIndex = MOCK_LEAVES_DB.findIndex(l => l.id === id);
        if (leaveIndex > -1) {
            MOCK_LEAVES_DB[leaveIndex].status = status;
            setLeaves([...MOCK_LEAVES_DB]);
        }
    };

    const StatusBadge = ({ status }) => {
        const styles = { pending: 'bg-yellow-200 text-yellow-800', approved: 'bg-green-200 text-green-800', rejected: 'bg-red-200 text-red-800' };
        const text = { pending: 'قيد المراجعة', approved: 'موافق عليه', rejected: 'مرفوض' };
        return <span className={`px-2 py-1 text-xs font-semibold rounded-full ${styles[status]}`}>{text[status]}</span>;
    };

    return (
        <div className="space-y-8">
            {showConfirm && (
                <div className="fixed inset-0 bg-black bg-opacity-50 z-50 flex justify-center items-center">
                    <div className="bg-white p-6 rounded-lg shadow-xl w-full max-w-md">
                        <h3 className="text-xl font-bold mb-4">تأكيد الموافقة</h3>
                        <p>بالموافقة على هذا الطلب، سيتجاوز عدد الموظفين المجازين الحد المسموح به. هل أنت متأكد؟</p>
                        <div className="flex justify-end gap-4 mt-6">
                            <button onClick={() => setShowConfirm(false)} className="px-4 py-2 bg-gray-300 rounded-md">إلغاء</button>
                            <button onClick={confirmApproval} className="px-4 py-2 bg-red-600 text-white rounded-md">نعم، موافقة</button>
                        </div>
                    </div>
                </div>
            )}
            <div className="bg-white p-6 rounded-lg shadow-md">
                <h2 className="text-xl font-bold mb-4">طلب إجازة جديد</h2>
                {error && <p className="text-sm text-red-600 bg-red-100 p-3 rounded-md mb-4">{error}</p>}
                <form onSubmit={handleRequestLeave} className="grid grid-cols-1 md:grid-cols-4 gap-4 items-end">
                    <div className="md:col-span-1"><label>من تاريخ</label><input name="startDate" type="date" className="w-full p-2 border rounded-md mt-1" required /></div>
                    <div className="md:col-span-1"><label>إلى تاريخ</label><input name="endDate" type="date" className="w-full p-2 border rounded-md mt-1" required /></div>
                    <div className="md:col-span-1"><label>السبب</label><input name="reason" type="text" className="w-full p-2 border rounded-md mt-1" required /></div>
                    <button type="submit" className="bg-indigo-500 text-white p-2 rounded-md h-10">إرسال الطلب</button>
                </form>
            </div>
            <div className="bg-white p-6 rounded-lg shadow-md">
                <h2 className="text-xl font-bold mb-4">طلباتي السابقة</h2>
                <div className="space-y-3">{myRequests.map(req => (<div key={req.id} className="flex justify-between items-center p-3 bg-gray-50 rounded-md"><div>من {req.startDate} إلى {req.endDate} ({req.reason})</div><StatusBadge status={req.status} /></div>))}</div>
            </div>
            {currentUser.role === 'manager' && (
                <div className="bg-white p-6 rounded-lg shadow-md">
                    <h2 className="text-xl font-bold mb-4">مراجعة طلبات الإجازات</h2>
                    <div className="space-y-3">
                        {leaves.filter(r => r.status === 'pending').map(req => (
                            <div key={req.id} className="p-3 bg-gray-50 rounded-md flex justify-between items-center">
                                <div><p className="font-semibold">{req.userName}</p><p className="text-sm text-gray-600">من {req.startDate} إلى {req.endDate}</p></div>
                                <div className="flex space-x-2 space-x-reverse">
                                    <button onClick={() => handleApprovalClick(req)} className="text-sm bg-green-500 text-white px-3 py-1 rounded-md">موافقة</button>
                                    <button onClick={() => updateRequestStatus(req.id, 'rejected')} className="text-sm bg-red-500 text-white px-3 py-1 rounded-md">رفض</button>
                                </div>
                            </div>
                        ))}
                         {leaves.filter(r => r.status === 'pending').length === 0 && <p className="text-gray-500">لا توجد طلبات معلقة.</p>}
                    </div>
                </div>
            )}
        </div>
    );
}

function AdminPanel({ allUsers, setAllUsers, setTasks }) {
    const [message, setMessage] = useState({ text: '', type: '' });
    const [newTask, setNewTask] = useState({ title: '', description: '', projectName: '', assignedTo: '', startDate: '', endDate: '' });
    const [newUser, setNewUser] = useState({ name: '', username: '', password: '' });
    const [editingUser, setEditingUser] = useState(null);

    const employees = allUsers.filter(u => u.role === 'employee');

    // --- Task Management ---
    const handleTaskInputChange = (e) => {
        const { name, value } = e.target;
        setNewTask(prev => ({ ...prev, [name]: value }));
    };

    const handleAddTask = (e) => {
        e.preventDefault();
        // ... (same as previous version)
    };

    // --- User Management ---
    const handleUserInput = (e) => {
        const { name, value } = e.target;
        setNewUser(prev => ({ ...prev, [name]: value }));
    };
    
    const handleAddUser = (e) => {
        e.preventDefault();
        setMessage({ text: '', type: '' });
        const username = newUser.username.toLowerCase().trim();
        if (MOCK_USERS_DB[username]) {
            setMessage({ text: 'اسم المستخدم هذا مستخدم بالفعل.', type: 'error' });
            return;
        }
        const newUserObj = {
            id: `employee-${username}`,
            name: newUser.name,
            username: username,
            role: 'employee',
            password: newUser.password
        };
        MOCK_USERS_DB[username] = newUserObj;
        setAllUsers(Object.values(MOCK_USERS_DB));
        setMessage({ text: `تمت إضافة الموظف ${newUser.name} بنجاح.`, type: 'success' });
        setNewUser({ name: '', username: '', password: '' });
    };

    const handleDeleteUser = (username) => {
        if (window.confirm(`هل أنت متأكد من حذف الموظف ${username}؟`)) {
            delete MOCK_USERS_DB[username];
            setAllUsers(Object.values(MOCK_USERS_DB));
            setMessage({ text: `تم حذف الموظف ${username}.`, type: 'success' });
        }
    };
    
    const handleEditUser = (user) => {
        const newName = prompt("أدخل الاسم الكامل الجديد:", user.name);
        const newPassword = prompt("أدخل كلمة المرور الجديدة (اتركها فارغة لعدم التغيير):");
        if (newName) {
            MOCK_USERS_DB[user.username].name = newName;
        }
        if (newPassword) {
            MOCK_USERS_DB[user.username].password = newPassword;
        }
        setAllUsers(Object.values(MOCK_USERS_DB));
        setMessage({ text: `تم تحديث بيانات ${user.name}.`, type: 'success' });
    };


    return (
        <div className="space-y-8">
            <h1 className="text-3xl font-bold">لوحة تحكم الإدارة</h1>
            {message.text && <div className={`p-3 rounded-md text-white ${message.type === 'success' ? 'bg-green-500' : 'bg-red-500'}`}>{message.text}</div>}
            
            {/* User Management Section */}
            <div className="p-6 border rounded-lg bg-white shadow-md">
                <h3 className="text-xl font-semibold mb-4">إدارة الموظفين</h3>
                <div className="grid md:grid-cols-2 gap-8">
                    {/* Add User Form */}
                    <div>
                        <h4 className="font-semibold mb-3">إضافة موظف جديد</h4>
                        <form onSubmit={handleAddUser} className="space-y-4">
                            <input name="name" type="text" placeholder="الاسم الكامل" value={newUser.name} onChange={handleUserInput} className="w-full p-2 border rounded-md" required />
                            <input name="username" type="text" placeholder="اسم المستخدم (للدخول)" value={newUser.username} onChange={handleUserInput} className="w-full p-2 border rounded-md" required />
                            <input name="password" type="password" placeholder="كلمة المرور" value={newUser.password} onChange={handleUserInput} className="w-full p-2 border rounded-md" required />
                            <button type="submit" className="w-full bg-blue-600 text-white p-2 rounded-md hover:bg-blue-700">إضافة موظف</button>
                        </form>
                    </div>
                    {/* User List */}
                    <div>
                        <h4 className="font-semibold mb-3">قائمة الموظفين</h4>
                        <div className="space-y-2 max-h-64 overflow-y-auto">
                            {employees.map(user => (
                                <div key={user.id} className="flex justify-between items-center p-2 bg-gray-50 rounded-md">
                                    <span>{user.name} (@{user.username})</span>
                                    <div className="flex gap-2">
                                        <button onClick={() => handleEditUser(user)} className="text-gray-500 hover:text-blue-600"><EditIcon /></button>
                                        <button onClick={() => handleDeleteUser(user.username)} className="text-gray-500 hover:text-red-600"><DeleteIcon /></button>
                                    </div>
                                </div>
                            ))}
                        </div>
                    </div>
                </div>
            </div>

            {/* Task Management Section */}
            <div className="p-6 border rounded-lg bg-white shadow-md">
                <h3 className="text-xl font-semibold mb-3">إضافة مهمة جديدة للفريق</h3>
                <form onSubmit={handleAddTask} className="space-y-4">
                    {/* ... Task form remains the same */}
                    <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <input name="title" type="text" placeholder="عنوان المهمة" value={newTask.title} onChange={handleTaskInputChange} className="w-full p-2 border rounded-md" required />
                        <input name="projectName" type="text" placeholder="اسم المشروع" value={newTask.projectName} onChange={handleTaskInputChange} className="w-full p-2 border rounded-md" required />
                    </div>
                    <textarea name="description" placeholder="شرح المهمة..." value={newTask.description} onChange={handleTaskInputChange} className="w-full p-2 border rounded-md" required />
                    <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
                        <select name="assignedTo" value={newTask.assignedTo} onChange={handleTaskInputChange} className="w-full p-2 border rounded-md bg-white" required>
                            <option value="">إسناد إلى...</option>
                            {employees.map(user => <option key={user.id} value={user.id}>{user.name}</option>)}
                        </select>
                        <input name="startDate" type="date" title="تاريخ البدء" value={newTask.startDate} onChange={handleTaskInputChange} className="w-full p-2 border rounded-md" required />
                        <input name="endDate" type="date" title="تاريخ التسليم" value={newTask.endDate} onChange={handleTaskInputChange} className="w-full p-2 border rounded-md" required />
                    </div>
                    <button type="submit" className="w-full bg-indigo-600 text-white p-2 rounded-md hover:bg-indigo-700">إضافة المهمة</button>
                </form>
            </div>
        </div>
    );
}

export default App;


