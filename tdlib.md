### 1.8.0 版本更新 (2021年12月29日)

* **将用户、基本群组和超级群组的标识符类型从 `int32` 更改为 `int53`。**
* **简化聊天列表加载，并移除误用 `getChats` 方法的可能性：**
  - 将方法 `getChats` 重命名为 `loadChats`。
  - 从 `loadChats` 方法中移除参数 `offset_order` 和 `offset_chat_id`。聊天现在总是从列表中已知的最后一个聊天开始加载。
  - 将 `loadChats` 方法的返回类型更改为 `ok`。如果未加载任何聊天，则返回404错误。聊天列表中的顺序必须通过更新 `updateChatPosition`、`updateChatLastMessage` 和 `updateChatDraftMessage` 进行维护。
  - 添加便捷方法 `getChats`，用于从聊天列表开头返回请求的聊天数量，适用于不需要保持列表一致性状态的情况。
* **添加挂接 TDLib 日志消息的功能：**
  - 向 JSON 接口添加函数 `td_set_log_message_callback`。
  - 向 `ClientManager` 类添加函数 `set_log_message_callback`。
  - 通过 C++/CX 向 UWP 原生包装器添加函数 `SetLogMessageCallback`。
  - 在 JSON 接口中弃用函数 `td_set_log_fatal_error_callback`，改用函数 `td_set_log_message_callback`。
  - 弃用函数 `Log::set_fatal_error_callback`，改用函数 `ClientManager::set_log_message_callback`。
* **添加支持以用户拥有的公共频道身份发送消息的功能：**
  - 向 `chat` 类添加字段 `message_sender_id`，包含当前选择的消息发送者的标识符。
  - 添加更新 `updateChatMessageSender`。
  - 添加方法 `getChatAvailableMessageSenders`，返回聊天中可用的消息发送者列表。
  - 添加方法 `setChatMessageSender`，更改当前选择的消息发送者。
  - 向 `messageSendingStateFailed` 类添加字段 `need_another_sender`。如果为 true，则需要向用户显示警报，提示消息将以另一发送者的身份重新发送。
  - 使用方法 `deleteChatMessagesBySender` 替换方法 `deleteChatMessagesFromUser`，其参数类型为 `MessageSender` 而非用户标识符。
  - 使用更新 `updateChatAction` 替换更新 `updateUserChatAction`，其源为 `MessageSender` 而非用户标识符。
* **新增在其他超级群组和频道中封禁超级群组和频道的功能：**
  - 在 `chatMember` 和 `chatEventMemberRestricted` 类中将字段 `user_id` 替换为字段 `member_id`。
  - 在 `setChatMemberStatus` 和 `getChatMember` 方法中将参数 `user_id` 替换为 `member_id`。
* **改进对动画表情的支持：**
  - 添加 `animatedEmoji` 类，包含有关动画表情的信息。
  - 添加 `messageAnimatedEmoji` 类至消息内容类型。
  - 添加方法 `clickAnimatedEmojiMessage`，用于点击动画表情时调用。
  - 添加更新 `updateAnimatedEmojiMessageClicked`，当需要播放大动画贴纸时接收。
  - 添加 `chatActionWatchingAnimations` 类至聊天操作类型。
  - 添加方法 `getAnimatedEmoji`，返回与给定表情符号对应的动画表情。
  - 添加可写选项 "disable_animated_emoji"。
  - 移除选项 "animated_emoji_sticker_set_name"。
* **新增所有聊天类型的自动消息删除功能：**
  - 向 `chat` 类添加字段 `message_ttl`。
  - 添加更新 `updateChatMessageTtl`。
  - 添加方法 `setChatMessageTtl`。
  - 添加类 `chatEventMessageTtlChanged`，表示在聊天事件日志中更改了 `message_ttl` 字段。
  - 移除 `secretChat` 类中的字段 `ttl`，替换为 `chat` 类中的 `message_ttl` 字段。
  - 使用方法 `setChatMessageTtl` 替换方法 `sendChatSetTtlMessage`。
* **改进字段类型 `MessageSender` 的字段名称：**
  - 在 `message` 和 `notificationTypeNewPushMessage` 类中将字段 `sender` 重命名为 `sender_id`。
  - 在 `searchChatMessages`、`addLocalMessage` 和 `toggleMessageSenderIsBlocked` 方法中将参数 `sender` 重命名为 `sender_id`。
  - 在 `messageReplyInfo` 类中将字段 `recent_repliers` 重命名为 `recent_replier_ids`。
  - 在 `messageProximityAlertTriggered` 类中将字段 `traveler` 重命名为 `traveler_id`。
  - 在 `messageProximityAlertTriggered` 类中将字段 `watcher` 重命名为 `watcher_id`。
* **改进了对机器人支付的支持：**
  - 允许通过在 `inputInlineQueryResultAnimation` 等类的字段 `input_message_content` 中使用 `inputMessageInvoice`，作为内联查询结果发送发票。
  - 允许将发票消息发送到基本群组、超级群组和频道聊天。
  - 允许机器人发送可转发的发票，通过在 `inputMessageInvoice` 类中指定空字段 `start_parameter`。
  - 向 `messagePaymentSuccessful` 类添加字段 `invoice_chat_id`。
  - 向 `paymentForm` 类添加字段 `id`，包含唯一支付表单标识符。
  - 向 `sendPaymentForm` 方法添加参数 `payment_form_id`。
  - 向 `paymentForm` 类添加字段 `seller_bot_user_id` 和 `payments_provider_user_id`，分别表示卖家和支付提供方机器人的用户标识符。
  - 向 `invoice` 类添加字段 `max_tip_amount` 和 `suggested_tip_amounts`。
  - 向 `sendPaymentForm` 方法添加参数 `tip_amount`。
  - 向 `paymentReceipt` 类添加字段 `tip_amount`。
  - 将类 `inputCredentialsAndroidPay` 重命名为 `inputCredentialsGooglePay`。
  - 添加 `paymentFormTheme` 类，包含支付表单的颜色设置。
  - 向 `getPaymentForm` 方法添加参数 `theme`。
  - 从 `messagePaymentSuccessfulBot` 类中移除字段 `invoice_message_id`。
* **添加方法 `deleteChat`，用于完全删除聊天及所有消息。**
* **用 `deleteChat` 方法替换 `deleteSupergroup` 方法。**
* **在 `getProxyLink` 方法中更改返回结果类型为 `httpUrl` 类而非 `text` 类。**
* **移除对秘密聊天层级小于73的支持。**
* **新增对赞助消息的支持：**
  - 添加 `sponsoredMessage` 类。
  - 添加方法 `getChatSponsoredMessage`。
  - 允许将赞助消息的标识符传递给 `viewMessages`。该方法必须在整个赞助消息文本（不包括按钮）显示在屏幕上时调用。
* **改进对视频聊天的支持：**
  - 添加 `groupCall` 类，表示群组通话。
  - 添加方法 `getGroupCall` 以获取群组通话信息。
  - 添加更新 `updateGroupCall`。
  - 添加 `videoChat` 类，表示绑定到聊天的群组视频通话。
  - 向 `chat` 类添加字段 `video_chat`。
  - 添加更新 `updateChatVideoChat`。
  - 添加 `messageVideoChatScheduled`、`messageVideoChatStarted` 和 `messageVideoChatEnded` 类至消息内容类型。
  - 添加 `messageInviteVideoChatParticipants` 类至消息内容类型。
  - 向 `chatMemberStatusAdministrator` 类添加字段 `can_manage_video_chats`。
  - 添加 `groupCallId` 类。
  - 添加方法 `createVideoChat`，用于创建视频通话。
  - 添加方法 `startScheduledGroupCall`。
  - 添加方法 `toggleGroupCallEnabledStartNotification`。
  - 添加方法 `joinGroupCall`。
  - 添加方法 `leaveGroupCall`。
  - 添加方法 `endGroupCall`。
  - 添加方法 `toggleGroupCallIsMyVideoEnabled`。
  - 添加方法 `toggleGroupCallIsMyVideoPaused`。
  - 添加方法 `startGroupCallScreenSharing`、`toggleGroupCallScreenSharingIsPaused` 和 `endGroupCallScreenSharing`，用于管理群组通话期间的屏幕共享。
  - 添加方法 `setGroupCallTitle`。
  - 添加方法 `toggleGroupCallMuteNewParticipants`。
  - 添加 `groupCallVideoSourceGroup` 和 `groupCallParticipantVideoInfo` 类，用于描述可用的视频流。
  - 添加 `groupCallParticipant` 类。
  - 添加更新 `updateGroupCallParticipant`。
  - 添加方法 `loadGroupCallParticipants`。
  - 添加方法 `toggleGroupCallParticipantIsHandRaised`。
  - 添加方法 `setGroupCallParticipantIsSpeaking`。
  - 添加方法 `toggleGroupCallParticipantIsMuted`。
  - 添加方法 `setGroupCallParticipantVolumeLevel`。
  - 向类 `groupCall` 和 `groupCallParticipant` 添加字段 `audio_source_id` 和 `screen_sharing_audio_source_id`。
  - 添加方法 `inviteGroupCallParticipants`。
  - 添加 `videoChatParticipant` 类。
  - 向类 `groupCall` 和 `groupCallParticipant` 添加字段 `is_my_video_enabled` 和 `is_my_video_paused`，用于更好地管理个人视频流状态。
### 1.8.0 版本更新 (2021年12月29日)

* **新增支持过期的聊天邀请链接：**
  - 向 `chatInviteLink` 类中添加字段 `is_primary`。主要邀请链接不能有名称、过期日期或使用限制。每个具有 `can_invite_users` 权限的管理员在任何时刻只能有一个主要邀请链接。
  - 向 `chatInviteLink` 类中添加字段 `name`。
  - 向 `chatInviteLink` 类中添加字段 `creator_user_id`。
  - 向 `chatInviteLink` 类中添加字段 `date`，包含链接创建日期。
  - 向 `chatInviteLink` 类中添加字段 `edit_date`，包含链接最后编辑日期。
  - 向 `chatInviteLink` 类中添加字段 `expiration_date` 和 `member_limit`，限制链接的使用。
  - 向 `chatInviteLink` 类中添加字段 `member_count`。
  - 向 `chatInviteLink` 类中添加字段 `is_revoked`。
  - 将 `basicGroupFullInfo` 和 `supergroupFullInfo` 类中的 `invite_link` 字段类型更改为 `chatInviteLink`。
  - 向 `chatInviteLinkInfo` 类中添加字段 `description`，包含聊天的描述。
  - 将方法 `generateChatInviteLink` 替换为 `replacePrimaryChatInviteLink`。
  - 添加方法 `createChatInviteLink` 用于创建新的邀请链接。
  - 添加方法 `editChatInviteLink` 用于编辑非主要邀请链接。
  - 添加方法 `revokeChatInviteLink`。
  - 添加方法 `deleteRevokedChatInviteLink`。
  - 添加方法 `deleteAllRevokedChatInviteLink`。
  - 添加方法 `getChatInviteLink`。
  - 添加 `chatInviteLinks` 类，包含邀请链接的列表。
  - 添加方法 `getChatInviteLinks`。
  - 添加 `chatInviteLinkCount` 和 `chatInviteLinkCounts` 类。
  - 添加方法 `getChatInviteLinkCounts`，返回聊天管理员创建的邀请链接数量。
  - 添加 `chatInviteLinkMember` 和 `chatInviteLinkMembers` 类。
  - 添加方法 `getChatInviteLinkMembers`。
  - 向 `chatEventLogFilters` 类中添加字段 `invite_link_changes`。
  - 添加 `chatEventMemberJoinedByInviteLink` 类，表示用户通过邀请链接加入聊天的聊天事件。
  - 添加 `chatEventInviteLinkEdited` 类到聊天事件类型。
  - 添加 `chatEventInviteLinkRevoked` 类到聊天事件类型。
  - 添加 `chatEventInviteLinkDeleted` 类到聊天事件类型。
  - 添加 `chatActionBarInviteMembers` 类到聊天操作栏类型。

* **新增支持创建加入请求的聊天邀请链接：**
  - 添加 `messageChatJoinByRequest` 类到消息内容类型。
  - 添加 `pushMessageContentChatJoinByRequest` 类到推送消息内容类型。
  - 向 `chat` 类中添加字段 `pending_join_requests`。
  - 添加 `chatJoinRequestsInfo` 类，包含有关聊天的待处理加入请求的基本信息。
  - 添加更新 `updateChatPendingJoinRequests`。
  - 向 `chatInviteLink` 和 `chatInviteLinkInfo` 类中添加字段 `creates_join_request`。
  - 向 `chatInviteLink` 类中添加字段 `pending_join_request_count`。
  - 添加 `chatJoinRequest` 类，描述发送加入请求的用户。
  - 添加 `chatJoinRequests` 类，包含请求加入聊天的列表。
  - 添加方法 `getChatJoinRequests`。
  - 添加方法 `processChatJoinRequest` 用于处理加入聊天的请求。
  - 添加方法 `processChatJoinRequests` 用于处理所有加入聊天的请求。
  - 添加 `chatActionBarJoinRequest` 类到聊天操作栏类型。
  - 添加 `chatEventMemberJoinedByRequest` 类，表示在聊天事件日志中批准加入聊天的用户。
  - 添加更新 `updateNewChatJoinRequest` 供机器人使用。

* **新增查看小型群聊中外发消息的查看者的功能：**
  - 添加方法 `getMessageViewers`。
  - 向 `message` 类中添加字段 `can_get_viewers`。
### 1.9.0 版本更新 (2022年3月15日)

* **新增 `forwardMessages` 方法的 `only_preview` 参数，用于接收转发消息的预览。**
* **新增方法 `getRecentlyOpenedChats`，返回最近打开的聊天列表。**
* **增加最近找到的聊天数量存储至 50。**
* **新增按天拆分聊天消息信息的功能：**
  - 添加 `messageCalendarDay` 类，表示特定日期发送的找到的消息。
  - 添加 `messageCalendar` 类，表示按天拆分的找到的消息。
  - 添加可写选项 `utc_time_offset`，包含用于按天拆分消息的 UTC 时间偏移量。该选项在每个 TDLib 实例启动时自动重置，因此仅在执行期间更改时间偏移量时手动设置。
  - 添加方法 `getChatMessageCalendar`，返回按天拆分的聊天消息信息。
* **新增在稀疏位置获取指定类型消息的功能，以实现超高速滚动：**
  - 添加 `messagePosition` 类，包含特定聊天历史位置中消息的标识符和发送日期。
  - 添加 `messagePositions` 类，包含消息位置的列表。
  - 添加方法 `getChatSparseMessagePositions`。
* **向 `chatMemberStatusAdministrator` 类添加字段 `can_manage_chat`，允许在没有额外权限的情况下提升聊天管理员。**
* **改进对机器人命令的支持：**
  - 机器人命令实体在没有机器人的聊天中自动隐藏。
  - 添加 `botCommands` 类，表示机器人命令的列表。
  - 向 `userFullInfo` 类中添加字段 `commands`，包含在与机器人的私聊中使用的命令列表。
  - 向 `basicGroupFullInfo` 和 `supergroupFullInfo` 类中添加字段 `bot_commands`。
  - 移除 `botInfo` 类。
  - 移除 `userFullInfo` 和 `chatMember` 类中的 `bot_info` 字段。
  - 添加 `BotCommandScope` 类。
  - 添加方法 `setCommands`、`deleteCommands`、`getCommands` 供机器人使用。
* **新增只读选项 `suggested_video_note_length`、`suggested_video_note_video_bitrate` 和 `suggested_video_note_audio_bitrate`，包含建议的视频笔记编码参数。**
* **新增只读选项 `channel_bot_user_id`，包含在过时客户端中显示为频道代发消息的机器人的标识符。**
* **新增获取实际值的功能，使用方法 `getOption` 获取选项 `is_location_visible` 的值，以防该值在其他设备上更改。**
* **向 `profilePhoto` 类添加字段 `minithumbnail`，表示个人资料照片的缩略图。**
* **向 `chatPhotoInfo` 类添加字段 `minithumbnail`，表示聊天照片的缩略图。**
* **新增对贴纸轮廓的支持：**
  - 向 `sticker` 类添加字段 `outline`。
  - 向 `stickerSet` 和 `stickerSetInfo` 类添加字段 `thumbnail_outline`。
  - 添加 `closedVectorPath` 类，表示封闭的矢量路径。
  - 添加 `VectorPathCommand` 类，表示封闭矢量路径的一条边。
  - 添加 `point` 类，表示笛卡尔平面上的一个点。
* **新增对受保护内容的聊天和消息的支持：**
  - 向 `chat` 类添加字段 `has_protected_content`。
  - 添加更新 `updateChatHasProtectedContent`。
  - 添加方法 `toggleChatHasProtectedContent`。
  - 添加 `chatEventHasProtectedContentToggled` 类，表示聊天事件日志中 `has_protected_content` 设置的更改。
  - 向 `message` 类添加字段 `can_be_saved`。
* **新增对广播群组的支持，即没有成员数量限制的超级群组，其中仅管理员可以发送消息：**
  - 向 `supergroup` 类添加字段 `is_broadcast_group`。
  - 添加方法 `toggleSupergroupIsBroadcastGroup`。超级群组转换为广播群组后无法撤销。
  - 添加 `suggestedActionConvertToBroadcastGroup` 类，表示将超级群组转换为广播群组的建议。
* **改进聊天报告：**
  - 向 `reportChat` 方法添加参数 `text`，允许为所有聊天报告原因添加附加详细信息。
  - 移除 `chatReportReasonCustom` 类中的 `text` 字段。
  - 添加方法 `reportChatPhoto` 用于报告聊天照片。
* **新增对被报告为假冒用户和聊天的支持：**
  - 向 `user` 类添加字段 `is_fake`。
  - 向 `supergroup` 类添加字段 `is_fake`。
  - 添加 `chatReportReasonFake` 类到聊天报告原因类型。
* **添加 `inlineKeyboardButtonTypeUser` 类到内联键盘按钮类型。**
* **向 `replyMarkupForceReply` 和 `replyMarkupShowKeyboard` 类添加字段 `input_field_placeholder`。**
* **新增消息中的媒体时间戳实体的支持：**
  - 添加 `textEntityTypeMediaTimestamp` 类到文本实体类型。
  - 向 `message` 类添加字段 `has_timestamped_media`，描述媒体时间戳实体是否指向消息本身或引用的消息。
  - 向 `getMessageLink` 方法添加参数 `media_timestamp`，以支持创建带有给定媒体时间戳的消息链接。
  - 向 `message` 类添加字段 `can_get_media_timestamp_links`。
  - 向 `messageLinkInfo` 类添加字段 `media_timestamp`，用于处理带有指定媒体时间戳的消息链接。
### 1.10.0 版本更新 (2022年6月1日)

* **新增更改活动会话属性的功能：**
  - 向 `session` 类添加字段 `can_accept_secret_chats`。
  - 添加方法 `toggleSessionCanAcceptSecretChats`。
  - 向 `session` 类添加字段 `can_accept_calls`。
  - 添加方法 `toggleSessionCanAcceptCalls`。
  - 向 `sessions` 类添加字段 `inactive_session_ttl_days`。
  - 添加方法 `setInactiveSessionTtl`。

* **新增手机号码验证的新方式：**
  - 添加 `authenticationCodeTypeMissedCall` 类，描述通过已取消电话向指定号码传递的验证代码。拨打电话的最后几位数字是用户必须手动输入的代码。
  - 向 `phoneNumberAuthenticationSettings` 类添加字段 `allow_missed_call`。
  - 新增只读选项 `authentication_token`，可以在注销时获取，包含用于后续授权的身份验证令牌。
  - 向 `phoneNumberAuthenticationSettings` 类添加字段 `authentication_tokens`。

* **新增从活动会话重置密码的支持：**
  - 添加 `ResetPasswordResult` 类和 `resetPassword` 方法。
  - 添加方法 `cancelPasswordReset`，可用于取消待处理的密码重置。
  - 向 `passwordState` 类添加字段 `pending_reset_date`。

* **在通过恢复电子邮件地址找回丢失密码后设置新的两步验证密码的功能：**
  - 向 `recoverAuthenticationPassword` 和 `recoverPassword` 方法添加参数 `new_password` 和 `new_hint`。
  - 添加方法 `checkAuthenticationPasswordRecoveryCode`。
  - 添加方法 `checkPasswordRecoveryCode`。

* **新增 `chatActionChoosingSticker` 类到聊天动作类型。**
* **新增 `backgroundFillFreeformGradient` 类到背景填充类型。**
* **向 `backgroundTypePattern` 类添加字段 `is_inverted`，用于深色主题的反转图案。**

* **新增聊天主题的支持：**
  - 向 `chat` 类添加字段 `theme_name`。
  - 添加方法 `setChatTheme`。
  - 添加更新 `updateChatTheme`，在聊天中主题更改时接收。
  - 添加 `messageChatSetTheme` 类到消息内容类型。
  - 添加 `pushMessageContentChatSetTheme` 类到推送消息内容类型。
  - 添加 `themeSettings` 类，表示基本主题设置。
  - 添加 `chatTheme` 类，表示聊天主题。
  - 添加更新 `updateChatThemes`，在可用聊天主题列表更改时接收。

* **新增普通用户创建贴纸集的能力：**
  - 普通用户可以使用 `uploadStickerFile` 和 `createNewStickerSet` 方法。
  - 将 `uploadStickerFile` 方法中的参数 `png_sticker` 替换为类型为 `InputSticker` 的参数 `sticker`。
  - 向 `createNewStickerSet` 方法添加参数 `source`。
  - 添加方法 `getSuggestedStickerSetName`。
  - 添加 `CheckStickerSetNameResult` 类和方法 `checkStickerSetName`，用于在创建贴纸集之前检查贴纸集名称。

* **新增从外部来源导入聊天历史的支持：**
  - 添加方法 `importMessages`。
  - 添加方法 `getMessageImportConfirmationText`。
  - 添加 `MessageFileType` 类和方法 `getMessageFileType`，用于检查导出的消息历史文件格式是否受支持。
  - 添加 `messageForwardOriginMessageImport` 类到导入消息的转发消息来源类型。
  - 向 `createNewSupergroupChat` 方法添加参数 `for_import`，在创建聊天以进行后续消息历史导入时需要设置为 true。

* **新增新的建议操作类型：**
  - 添加 `suggestedActionSetPassword` 类，建议用户在指定天数内设置两步验证密码，以便能够再次登录。
  - 添加 `suggestedActionCheckPassword` 类，建议用户检查他们是否仍然记得两步验证密码。
  - 添加 `suggestedActionViewChecksHint` 类，建议用户查看关于发送消息的单一和双重勾号含义的提示。

* **新增方法 `getSuggestedFileName`，返回在给定目录中保存文件的建议名称。**
* **新增方法 `deleteAllCallMessages`。**
* **新增方法 `deleteChatMessagesByDate`，可用于删除指定日期之间的所有消息。**
### 1.11.0 版本更新 (2022年9月1日)

* **新增字段：**
  - 向 `messageThreadInfo` 类添加字段 `unread_message_count`。
  - 向 `userFullInfo` 类添加字段 `has_private_forwards`。
  - 向 `userFullInfo` 类添加字段 `description`，包含机器人的描述。
  - 向 `inputMessageSticker` 类添加字段 `emoji`，允许指定用于选择发送的贴纸的表情符号。

* **新增功能：**
  - 添加方法 `banChatMember`，可用于禁止聊天成员，取代方法 `setChatMemberStatus`，并允许撤回被禁止用户在基本组中的消息。
  - 允许使用方法 `setChatMemberStatus` 添加聊天成员。
  - 移除方法 `reportSupergroupSpam` 中的参数 `user_id`，现在可以同时报告来自不同发件人的消息。
  - 向 `webPageInstantView` 类添加字段 `feedback_link`。
  - 添加方法 `getApplicationDownloadLink`，返回下载官方 Telegram 应用程序的链接。

* **移除功能：**
  - 移除无用的搜索消息过滤器 `searchMessagesFilterCall` 和 `searchMessagesFilterMissedCall`。
  - 移除方法 `getChatStatisticsUrl`。
  - 移除方法 `getInviteText`，以支持方法 `getApplicationDownloadLink`。

* **更新和改进：**
  - 向更新 `updateNewInlineQuery` 添加字段 `chat_type`，用于机器人。
  - 添加更新 `updateChatMember` 用于机器人。
  - 改进 [TDLib 构建说明生成器](https://tdlib.github.io/td/build.html) 的外观。
  - 添加对 illumos 操作系统的支持。
  - 添加对真实 watchOS 设备的网络访问支持。
  - 添加对 OpenSSL 3.0 的支持。
  - 改进 iOS/watchOS/tvOS 构建示例，以生成通用 XCFramework。
  - 添加对 iOS/watchOS/tvOS 构建示例中 ARM64 模拟器的支持。
  - 为 Universal Windows Platform 的构建脚本添加选项 `-release_only`，允许仅以发布模式构建 TDLib SDK。

* **重新编写的绑定：**
  - 使用新 `ClientManager` 接口重新编写 .NET 本地绑定：
    - 将方法 `Client.send` 替换为必须在专用线程中调用一次的静态方法。`ClientResultHandler` 的回调将在该线程中为所有客户端调用。
    - 从 C++/CLI 本地绑定中移除函数 `Client.Dispose()`，`Client` 类型的对象不再需要显式处置。
  
  - 使用新 `ClientManager` 接口重新编写 C 绑定：
    - 将结构体 `TdRequest` 和 `TdResponse` 中的字段 `id` 重命名为 `request_id`。
    - 向结构体 `TdResponse` 添加字段 `client_id`。
    - 将方法 `TdCClientCreate` 替换为 `TdCClientCreateId`。
    - 在函数 `TdCClientSend` 中将参数 `instance` 替换为 `client_id`。
    - 添加方法 `TdCClientReceive` 和 `TdCClientExecute`。
    - 移除方法 `TdCClientSendCommandSync`、`TdCClientDestroy` 和 `TdCClientSetVerbosity`。










### 1.1.0 版本更新 (2018年1月31日)

* **日志管理：**
  - 方法 `td::Log::set_file_path` 和 `td_set_log_file_path` 现在返回是否成功的结果。
  - 新增方法 `td::Log::set_max_file_size` 和 `td_set_log_max_file_size`，用于限制 TDLib 日志的最大大小。
  - 新增方法 `td::Log::set_fatal_error_callback` 和 `td_set_log_fatal_error_callback`，提供致命错误的回调。

* **JNI 绑定：**
  - JNI 绑定现在是包无关的。使用 CMake 选项 `TD_ENABLE_JNI` 来启用 JNI 绑定。
  - 添加 Java 示例。请参阅 [README](example/java/README.md) 以获取构建和使用说明。

* **媒体标题中的文本实体支持：**
  - 新增类型 `formattedText`，包含带有实体的文本。
  - 用类型为 `formattedText` 的字段替换了所有字符串字段 `caption`。
  - 在 `messageText` 类中，将字段 `text` 和 `entities` 替换为字段 `text`（类型为 `formattedText`）。
  - 在 `inputMessageText` 类中，将字段 `text` 和 `entities` 替换为字段 `text`（类型为 `formattedText`）。
  - 在 `game` 类中，将字段 `text` 和 `text_entities` 替换为字段 `text`（类型为 `formattedText`）。
  - 从 `inputMessageText` 类中移除字段 `parse_mode`。
  - 添加同步方法 `parseTextEntities`。

* **消息更新：**
  - 现在对于所有发送的消息都会发送 `updateNewMessage`。
  - 当聊天中最后一条消息的任何字段发生更改时，会发送 `updateChatLastMessage`。

* **设备注册：**
  - 重新设计 `registerDevice` 方法：
    - 在 `registerDevice` 方法中添加参数 `other_user_ids` 以支持多个帐户。
    - 现在可以为 VoIP 推送、WNS、Web Push API、Tizen Push Service 指定 `DeviceToken`。
    - 在 App Sandbox 中添加对 Apple Push Notification Service 的支持。

* **聊天搜索：**
  - 新增方法 `searchChatsOnServer`，类似于 `searchChats`，但使用服务器搜索。
  - `searchChatsOnServer` 方法的结果现在从 `searchPublicChats` 的结果中排除，因此在调用 `searchPublicChats` 时，应同时调用 `searchChatsOnServer`（以及 `searchContacts`）以确保没有遗漏结果。

* **公共消息链接：**
  - 为 `getPublicMessageLink` 方法添加参数 `as_album`，以便获取媒体专辑的公共链接。
  - 向 `publicMessageLink` 类添加字段 `html`，包含消息/消息专辑嵌入的 HTML 代码。

* **文件下载管理：**
  - 为方法 `cancelDownloadFile` 添加参数 `only_if_pending`，允许保留已开始的下载。

* **聊天创建优化：**
  - 现在可以在不先调用 `getUser`/`getBasicGroup`/`getSupergroup`/`getSecretChat` 的情况下调用方法 `createPrivateChat`、`createBasicGroupChat`、`createSupergroupChat` 和 `createSecretChat`。
  - 为方法 `createPrivateChat`、`createBasicGroupChat` 和 `createSupergroupChat` 添加参数 `force`，允许在没有网络请求的情况下创建聊天。

* **其他改进：**
  - 多项优化和 bug 修复。
