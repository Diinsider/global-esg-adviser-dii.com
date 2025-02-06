# global-esg-adviser-dii.com
import SwiftUI

struct Message: Identifiable {
    let id = UUID()
    let content: String
    let isUser: Bool
}

struct ESGKnowledge {
    static let responses: [String: String] = [
        "ESG": "ESG（环境、社会与治理）是衡量企业可持续性的关键框架，全球化企业需关注：\n1. 碳排放与气候行动\n2. 供应链劳工标准\n3. 董事会多元化",
        "社区关系": "跨国社区关系管理要点：\n✅ 本土化雇佣政策\n✅ 文化遗产保护\n✅ 社区发展投资\n案例：某矿企在非洲建设学校医院提升社区接受度",
        "可持续发展": "全球化可持续战略应包含：\n🌍 清洁能源转型路线图\n🌍 循环经济模式设计\n🌍 生物多样性保护承诺\n参考标准：UN SDGs 12/13/15"
    ]
}

struct ChatView: View {
    @State private var messages: [Message] = []
    @State private var newMessage = ""
    
    var body: some View {
        VStack {
            ScrollView {
                ScrollViewReader { proxy in
                    LazyVStack {
                        ForEach(messages) { message in
                            MessageBubble(message: message)
                        }
                    }
                    .onChange(of: messages.count) { _ in
                        proxy.scrollTo(messages.last?.id, anchor: .bottom)
                    }
                }
            }
            
            HStack {
                TextField("输入ESG相关问题...", text: $newMessage)
                    .textFieldStyle(RoundedBorderTextFieldStyle())
                    .padding(.horizontal)
                
                Button(action: sendMessage) {
                    Image(systemName: "paperplane.fill")
                }
                .padding(.trailing)
            }
        }
    }
    
    private func sendMessage() {
        guard !newMessage.isEmpty else { return }
        
        // 用户消息
        messages.append(Message(content: newMessage, isUser: true))
        
        // 生成回复
        let response = generateResponse(for: newMessage)
        messages.append(Message(content: response, isUser: false))
        
        newMessage = ""
    }
    
    private func generateResponse(for query: String) -> String {
        let lowerQuery = query.lowercased()
        
        for (keyword, answer) in ESGKnowledge.responses {
            if lowerQuery.contains(keyword.lowercased()) {
                return answer
            }
        }
        
        return defaultResponse()
    }
    
    private func defaultResponse() -> String {
        return """
        🤖 我是ESG咨询助手，目前可解答以下领域问题：
        - ESG核心要素
        - 全球化社区关系管理
        - 可持续发展战略设计
        请输入关键词获取详细指南
        """
    }
}

struct MessageBubble: View {
    let message: Message
    
    var body: some View {
        HStack {
            if message.isUser {
                Spacer()
                Text(message.content)
                    .padding()
                    .background(Color.blue)
                    .foregroundColor(.white)
                    .cornerRadius(15)
            } else {
                Text(message.content)
                    .padding()
                    .background(Color.gray.opacity(0.2))
                    .foregroundColor(.black)
                    .cornerRadius(15)
                Spacer()
            }
        }
        .padding(.horizontal)
        .id(message.id)
    }
}

struct ContentView: View {
    var body: some View {
        NavigationView {
            ChatView()
                .navigationTitle("全球ESG助手")
        }
    }
}

// 预览
struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
