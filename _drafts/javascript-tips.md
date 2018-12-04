utc time to local time
	
	var utcTime = moment.utc('2018-11-22T03:20:02.52').toDate();
	var localTime = moment(utcTime).local().format('YYYY-MM-DD HH:mm:ss');
	alert(localTime);

