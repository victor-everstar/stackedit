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



  
 # Báo cáo hiện trạng dữ liệu sportmonks
 ## Hiện trạng dữ liệu
- Các dữ liệu đã có: các dữ liệu liên quan đến một mùa giải, bao gồm league, season, round, schedule, team, player, squad, score, standing, comment, news,. lineup,  big event trong trận (yellow card, red card, ....), event timeline (luồng diễn ra các event trong trận đấu),... 
- Dữ liệu còn thiếu: 
	- dữ liệu `latest_player_statistic` là dữ liệu phân tích các chỉ số của cầu thủ trong trận đấu.
	- thời điểm kết thúc trận đấu trong inplay
## Đánh giá dữ liệu
Dựa trên cấu trúc dữ liệu hiện tại, khi áp dụng vào để tính toán hệ thống các công thức cân bằng thì dữ liệu statistic của player theo trận đấu của sportmonks chỉ thiếu 2 trường:
- `passes_into_penalty_area`
- `block_defensive`


Hầu hết các dữ liệu quan trọng trong trận đấu đều xoay quanh `fixture` (trận đấu), và `type`: loại sự kiện diễn ra. Dựa vào đó có thể đánh giá các trường dữ liệu mà sportmonks cung cấp có phù hợp với hệ thống cân bằng hay không.

→ có thể cân nhắc thay bằng chỉ số khác.
→ các trường dữ liệu player_statistic (theo trận đấu) đáp ứng đủ để có thể tính điểm fantasy point theo trận đấu

## Các bước tiếp theo
- Q&A với sportmonks giải đáp các thắc mắc để lấy được dữ liệu `player_statistic` trong trận đấu → Nếu ok thì sẽ mua tiếp dữ liệu và xây dựng đánh giá thử thuật toán cân bằng
	- Nếu không oke thì chuyển qua phương án trong trận đấu chỉ cập nhật chỉ số khi có các big event, còn sẽ tính lại fantasy point sau trận đấu.
- Khảo sát thêm dữ liệu từ một số data provider khác: https://highlightly.net/, https://sportradar.com/ 

Bên sportmonks có phản hồi về các câu hỏi của mình rồi a, em tóm tắt qua
- Dữ liệu Player statistics thì được cung cấp theo 3 cấp :

	-   **Per Match**: thông qua việc include  `statistics`  trong fixture endpoint.
	    
	-   **Per Season**: lấy thông qua việc include  `statistics`  trong player endpoint và sử dụng filter  `filters[playerstatisticSeasons]`.
	    
	-   **Per Round**: Không hỗ trợ lấy player statistic theo round, tuy nhiên thì mình có thể lấy dữ liệu fixture data theo round của từng player và tự tổng hợp lại.
- Trong  API lấy dữ liệu realtime thì cũng trả về player_statistic thông qua `include=statistics` 
	- Khuyến nghị với livescores thì nên gọi lại api 1-2s một lần để lấy được latest data (theo khuyến nghị của s
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE5NDQyNDg2OTMsLTcwODg5Nzk5NiwtMT
gwMTA0OTQ5MSwtMTkxNjM4NDkxNiwtMTU5MDE2NTEyNywtMjA4
OTM2MjM0OCwtMjA2ODUxMTM1MSwtMTM0MzQxODUwOCwxNDgxND
k4MjExLC05MTAxMDc1MjMsMjExNTkyNDc1MCw1NDEzMzcwNjks
NDc1NTQyNjY0LDY0MzM4Mjk4NSwzNjMyODQwMjAsMTMxNzM3Mz
g5MSwxODkwMTk5MDQ5LDEyMTk2NDQyOTksMTczNDA2MjU4OCw1
MzQ2MDMxOTddfQ==
-->