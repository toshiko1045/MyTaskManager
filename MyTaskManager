//
// ---------- user settings ----------
const ACCESS_TOKEN    = 'XXXXXXXXXX...';
const EMAIL_USER      = "XXX@XXX.com"
const USER_ID         = "XXXXX..."
// ---------- user settings ----------
//

const CALENDAR_APP    =  CalendarApp.getCalendarById(EMAIL_USER);
const LINE_REPLY_URL  = 'https://api.line.me/v2/bot/message/reply';
const SPREADSHHET_APP = SpreadsheetApp.getActiveSpreadsheet();
const SHEET_ACTIVE    = SPREADSHHET_APP.getActiveSheet();


function getToDoList() {

  var ToDoList="\n";
  for(var i=1; SHEET_ACTIVE.getRange(i, 1).getValue()!="" ; i++){
    ToDoList += ("00" + i).slice(-2) + " " + SHEET_ACTIVE.getRange(i, 1).getValue() + "\n"
  }

  console.log(ToDoList);
  return ToDoList;
}
function setToDoList(msg) {
  var i=1
  for(i=1; SHEET_ACTIVE.getRange(i, 1).getValue()!="" ; i++){
    
  }
  SHEET_ACTIVE.getRange(i, 1).setValue(msg)
  return ("00"+(i)).slice(-2)+"に「" + msg + "」を追加しました.\n" + getToDoList()
}
function pushToDoList() {
  pushMessage(getToDoList());
}
function removeToDoList(index) {
  index = parseInt(index)
  var msg = SHEET_ACTIVE.getRange(index, 1).getValue()
  var ToDoList = []
  for(var i=1; SHEET_ACTIVE.getRange(i, 1).getValue()!="" ; i++){
    if(i!=index){
      ToDoList.push(SHEET_ACTIVE.getRange(i, 1).getValue())
    }
    SHEET_ACTIVE.getRange(i, 1).setValue("")
  }
  for(var i=0; i<ToDoList.length; i++){
    SHEET_ACTIVE.getRange(i+1, 1).setValue(ToDoList[i])
  }
  return "「" + msg + "」を削除しました.\n" + getToDoList()
}
function setTodoTrigger(setTimeTodoTrigger){
  SHEET_ACTIVE.getRange(2, 2).setValue(setTimeTodoTrigger);
}
function getCalenderEvent(){

  var newDate = new Date(); //今日の日付オブジェクト
  var calevents = CALENDAR_APP.getEventsForDay(newDate); //今日の予定の配列
  var todayMonth = newDate.getMonth() + 1; //今月
  var todayDate = newDate.getDate(); //今日の日
  var calmessage = [todayMonth + '月' + todayDate + '日' + 'の予定\n'];


  for (var i in calevents){
    var eventTitle = calevents[i].getTitle();
    var eventStart = Utilities.formatDate(calevents[i].getStartTime(), 'JST', 'HH:mm');
    var eventAll = eventStart + ' ' + eventTitle;
    calmessage.push(eventAll);
  }

  calmessage.push("\n以上")
  console.log(calmessage.join('\n'))
  return calmessage.join('\n'); 
}
function pushCalList() {
  pushMessage(getCalenderEvent());
}
function setCalTrigger(setTimeCalTrigger){
  SHEET_ACTIVE.getRange(2,3).setValue(setTimeCalTrigger);
}
function getStatus() {
  var status = SHEET_ACTIVE.getRange(1, 4).getValue();
   
  return status;
}
function setStatus(status) {
  SHEET_ACTIVE.getRange(1, 4).setValue(status);
}
function clearStatus() {
  SHEET_ACTIVE.getRange(1, 4).setValue("")
}
function setTrigger(hours, minutes, funcName) {
  try{
    var date = new Date();
    date.setHours(hours);
    date.setMinutes(minutes);

    if (new Date() > date) {
      date.setDate(date.getDate() + 1);
    }
    ScriptApp.newTrigger(funcName).timeBased().at(date).create();
  } catch(error) {
    return false;
  }
  return true;
}
function triggerPush(){
  var time_todo_trigger = parseInt(SHEET_ACTIVE.getRange(2,2).getValue());
  for(var i=1; i<=24/time_todo_trigger; i++){
    setTrigger(i*time_todo_trigger, 0, 'pushToDoList');  //トリガーセット
  }
  var time_cal_trigger = parseInt(SHEET_ACTIVE.getRange(2,3).getValue());
  setTrigger(time_cal_trigger,0,"pushCalList");
}
function pushMessage(msg) {
  var userId = USER_ID
  var postData = {
    "to": userId, 
    "messages": [{
      "type": "text",
      "text": msg,
    }]
  };
  var url = "https://api.line.me/v2/bot/message/push";
  var headers = {
    "Content-Type": "application/json",
    'Authorization': 'Bearer ' + ACCESS_TOKEN,
  };
  var options = {
    "method": "post",
    "headers": headers,
    "payload": JSON.stringify(postData)
  };
  var response = UrlFetchApp.fetch(url, options);
}
function reply(e, msg) {
  var repmsg = UrlFetchApp.fetch(LINE_REPLY_URL, {
    'headers': {
       'Content-Type': 'application/json; charset=UTF-8',
       'Authorization': 'Bearer ' + ACCESS_TOKEN,
    },
    'method': 'post',
    'payload': JSON.stringify({
      'replyToken': e,
      'messages': [{
        'type': 'text',
        'text': msg,
      }],
    }),
  });
  return repmsg.getResponseCode();
}
function doPost(e) {
  var json          = JSON.parse(e.postData.contents);
  var reply_Token   = json.events[0].replyToken;
  var Message_user  = json.events[0].message.text;
  var USER_ID       = json.events[0].source.userId;
  var Message_reply = "";

  if(json.events[0].source.type == 'user'){
    if(Message_user == 'ToDo取得'){
      Message_reply = getToDoList();
    }
    else if(Message_user == "Cal取得"){
      Message_reply = getCalenderEvent();
    }
    else if(Message_user == "ToDo追加"){
      Message_reply = "追加するToDoを入力してください."
      setStatus("pushToDo")
    }
    else if(Message_user == "ToDo設定"){
      Message_reply = "何時間置きにToDoリストを送信するか入力してください."
      setStatus("setTodoTime");
    }
    else if(Message_user == "Cal設定"){
      Message_reply = "何時にカレンダーを送信するか入力してください."
      setStatus("setCalTime");
    }
    else if(Message_user == "ToDo削除"){
      Message_reply = "削除するToDoの番号を入力してください"
      setStatus("popToDo");
    }
    else if(Message_user!="" && getStatus()=="pushToDo"){
      Message_reply = setToDoList(Message_user);
      clearStatus();
    }
    else if(Message_user!="" && getStatus()=="popToDo"){
      Message_reply = removeToDoList(Message_user);
      clearStatus();
    }
    else if(Message_user!="" && getStatus()=="setTodoTime"){
      setTodoTrigger(parseInt(Message_user));
      Message_reply = Message_user + "時間置きに設定しました.";
      clearStatus();
    }
    else if(Message_user!="" && getStatus()=="setCalTime"){
      setCalTrigger(parseInt(Message_user));
      Message_reply = Message_user + "時に設定しました.";
      clearStatus();
    }
    else{
      Message_reply = "もう一度入力してください";
    }
  } else {
    Message_reply = 'もう一度入力してください';
  }

  reply(reply_Token, Message_reply);
}



