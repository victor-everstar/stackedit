 - [x] Khi lấy team squad thì bổ sung lấy luôn statistic của player trong mùa giải đó `?include=player.statistics.details&filters=playerstatisticSeasons:21646`
- [ ] lấy dữ liệu `postmatchNews.lines;prematchNews.lines`
- [ ] lineup detail chưa đủ, thiếu từ trận 18842434 đến 18842659 (225 trận)
- [ ] Thiếu dữ liệu latest của cầu thủ `include=lineups.player.latest` ở API lấy player detail
	- `trophies.league;trophies.season;trophies.trophy;trophies.team;teams.team;statistics.details.type;statistics.team;statistics.season.league;latest.fixture.participants;latest.fixture.league;latest.fixture.scores;latest.details.type;nationality;detailedPosition;metadata.type`


# Câu hỏi
- Dữ liệu statistic của cầu thủ sẽ được tính lại theo mùa hay theo từng trận đấu hay theo round?
- Khi trận đấu diễn ra, tôi lấy player theo fixture thì player statisitc của player có được update tại thời điểm đó không ?
- Làm thế nào biết thời điểm trận đấu đã kết thúc (bao gồm cả bù giờ). Trong inplay có lấy được thông tin này không?
Đây là một số câu hỏi về dữ liệu mà e còn thắc mắc, e đã gửi mail + inbox trên IG của sportmonks để hỏi.


# Trong hệ thống công thức hiện tại, thiếu các trường

 1. `passes_into_penalty_area`
 2. `block_defensive`
 
 # Báo cáo hiện trạng dữ liệu sportmonks
 
- Các dữ liệu đã có: các dữ liệu liên quan đến một mùa giải, bao gồm league, season, round, schedule, team, player, squad, big event trong trận (yellow card, red card, ....). 

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE5MzY4NTY2ODQsLTE5MTYzODQ5MTYsLT
E1OTAxNjUxMjcsLTIwODkzNjIzNDgsLTIwNjg1MTEzNTEsLTEz
NDM0MTg1MDgsMTQ4MTQ5ODIxMSwtOTEwMTA3NTIzLDIxMTU5Mj
Q3NTAsNTQxMzM3MDY5LDQ3NTU0MjY2NCw2NDMzODI5ODUsMzYz
Mjg0MDIwLDEzMTczNzM4OTEsMTg5MDE5OTA0OSwxMjE5NjQ0Mj
k5LDE3MzQwNjI1ODgsNTM0NjAzMTk3LDE2Mjk2MzExNDddfQ==

-->