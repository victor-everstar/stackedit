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
 ## Hiện trạng dữ liệu
- Các dữ liệu đã có: các dữ liệu liên quan đến một mùa giải, bao gồm league, season, round, schedule, team, player, squad, big event trong trận (yellow card, red card, ....), event timeline (luồng diễn ra các event trong trận đấu),... 
- Dữ liệu còn thiếu: dữ liệu `latest_player_statistic` là dữ liệu phân tích các chỉ số của cầu thủ trong trận đấu.
## Đánh giá dữ liệu
Dựa trên cấu trúc dữ liệu hiện tại, khi áp dụng vào để tính toán hệ thống các công thức cân bằng thì dữ liệu statistic của player theo trận đấu của sportmonks chỉ thiếu 2 trường:
- `passes_into_penalty_area`
- `block_defensive`

→ có thể cân nhắc thay bằng chỉ số khác.
→ các trường dữ liệu player_statistic (theo trận đấu) đáp ứng đủ để có thể tính điểm fantasy point theo trận đấu

## Các bước tiếp theo
- Q&A với sportmonks giải đáp các thắc mắc để lấy được dữ liệu `player_statistic` trong trận đấu → Nếu ok thì sẽ mua tiếp dữ liệu và xây dựng đánh giá thử thuật toán cân bằng
- Khảo sát thêm dữ liệu từ một số data provider khác: https://highlightly.net/, https://sportradar.com/ 


<!--stackedit_data:
eyJoaXN0b3J5IjpbNjgwNjM2MzUxLC0xOTE2Mzg0OTE2LC0xNT
kwMTY1MTI3LC0yMDg5MzYyMzQ4LC0yMDY4NTExMzUxLC0xMzQz
NDE4NTA4LDE0ODE0OTgyMTEsLTkxMDEwNzUyMywyMTE1OTI0Nz
UwLDU0MTMzNzA2OSw0NzU1NDI2NjQsNjQzMzgyOTg1LDM2MzI4
NDAyMCwxMzE3MzczODkxLDE4OTAxOTkwNDksMTIxOTY0NDI5OS
wxNzM0MDYyNTg4LDUzNDYwMzE5NywxNjI5NjMxMTQ3XX0=
-->