```
package com.my.crawling;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import org.jsoup.Connection;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/Clawer/*")
public class ClawerController {
	static final String value_Emg_Msg = "응급메시지";
	static final String value_Bed_Orig = "일반";
	static final String value_Bed_Infa = "소아";
	static final String value_Bed_Iso = "음압격리";
	static final String value_Hosp_Main_Tel = "대표전화";
	static final String value_Hosp_Emg_Tel = "응급실전화";

	@RequestMapping(value = "/test")
	public @ResponseBody void test() throws Exception {
		int idx = 0;
//		String url = "https://portal.nemc.or.kr:444/medi_info/dashboards/dash_total_emer_org_popup_for_egen.do?&juso=&lon=&lat=&con=on&emogloca=12&emogdstr=1211&asort=A&asort=C&asort=D&rltmCd=O001&rltmCd=O002&rltmCd=O003&afterSearch=map&theme=BLACK&refreshTime=60&spreadAllMsg=allClose&searchYn=Y";
		// 부산 전체 url
		String url = "https://portal.nemc.or.kr:444/medi_info/dashboards/dash_total_emer_org_popup_for_egen.do?&juso=&lon=&lat=&con=on&emogloca=12&asort=A&asort=C&asort=D&rltmCd=O001&rltmCd=O002&rltmCd=O003&afterSearch=map&theme=BLACK&refreshTime=60&spreadAllMsg=allClose&searchYn=Y";
		// List : 병원 리스트
		// Map(1) : 병원 이름
		// Map(2) : 병원 정보
		List<Map<String, Map<String, String>>> hosp_Info = new ArrayList<Map<String, Map<String, String>>>();
		List<Map<String, ArrayList<String>>> hosp_Msg = new ArrayList<Map<String, ArrayList<String>>>();

		try {
			Connection conn = Jsoup.connect(url);
			Document html = conn.get();
			List<Element> list = new ArrayList<Element>();
			Elements dashBoards = html.getElementsByClass("sub_container");

			for (Element i : dashBoards) {
				// 병원 이름 Map
				Map<String, Map<String, String>> hosp_Info_Sim = new HashMap<String, Map<String, String>>();
				// 병원 정보 Map
				Map<String, String> hosp_Info_Tot = new HashMap<String, String>();

				List<String> hosp_Msg_Tmp_List = new ArrayList<String>();
				// 응급 메시지 Map
				Map<String, ArrayList<String>> hosp_Msg_Tmp = new HashMap<String, ArrayList<String>>();

				// 병원 이름 따기 S
				Element a_Tag = i.select("a").first();
				String[] arr = a_Tag.text().split(" ");
				String hosp_Name = arr[arr.length - 1];
				// 병원 이름 따기 E

				Element dashboard_Item = i.getElementById("area_dashboards").child(idx);
//				System.out.println(hosp_Name);
				Elements dashboard_Item_Item = i.getElementsByClass("item");

				// 응급메시지 처리
				Elements hosp_Emg_Msg = dashboard_Item.child(1).child(0).child(1).select("span div ul li.emer_row p");
				for (Element msg : hosp_Emg_Msg) {
					hosp_Msg_Tmp_List.add(String.valueOf(msg));
				}
				hosp_Msg_Tmp.put(hosp_Name, (ArrayList<String>) hosp_Msg_Tmp_List);
				hosp_Msg.add(hosp_Msg_Tmp);

				// 일반 병상
				String hosp_Bed_Orig = dashboard_Item.child(1).child(1).select("table").select("tbody")
						.select("tr td div.data_td_O001").text();
				// 소아 병상
				String hosp_Bed_Infa = dashboard_Item.child(1).child(1).select("table").select("tbody")
						.select("tr td div.data_td_O002").text();
				// 음압 병상
				String hosp_Bed_Iso = dashboard_Item.child(1).child(1).select("table").select("tbody")
						.select("tr td div.data_td_O003").text();
				hosp_Info_Tot.put(value_Bed_Orig, hosp_Bed_Orig);
				hosp_Info_Tot.put(value_Bed_Infa, hosp_Bed_Infa);
				hosp_Info_Tot.put(value_Bed_Iso, hosp_Bed_Iso);

				hosp_Info_Sim.put(hosp_Name, hosp_Info_Tot);

				hosp_Info.add(hosp_Info_Sim);

			}
			for (Map<String, Map<String, String>> i : hosp_Info) {
				System.out.println("================================================================");
				System.out.println(i.toString());
				for (int j = 0; j < hosp_Msg.size(); j++) {
					if (hosp_Msg.get(j).keySet().equals(i.keySet())) {
						System.out.println(hosp_Msg.get(j).toString());
					}
				}
				System.out.println("================================================================");
			}
		} catch (

		Exception e) {
			e.printStackTrace();
		}
	}
}

```
