# 定位类

​护士执行--打印\DHCCNursePrintComm\Nurse\NurseSheetLisLabel.cs​​

# 增加两个方法

```
        public void handleOrderItemGroups(List<NurseOrderItemGroup> orderItemGroups) {
            bool ifNew = false;
            List<NurseOrderItemGroup> newoOderItemGroups = new List<NurseOrderItemGroup>(); ;
            foreach (NurseOrderItemGroup group in orderItemGroups)
            {
                if (group.orderItems.Count >= 2) {
                    List<NurseOrderItem> orderItems = group.orderItems;
                    //把orderItems拆分成2为上线的List
                    List<List<NurseOrderItem>> orderItemsList = getListOrderItems(orderItems);
                    foreach (List<NurseOrderItem> currOrderItems in orderItemsList) {
                        NurseOrderItemGroup newGroup = new NurseOrderItemGroup();
                        newGroup.orderId = group.orderId;
                        newGroup.orderItemId = group.orderItemId;
                        newGroup.orderOriginId = group.orderOriginId;
                        newGroup.sameDateOrderGroups = group.sameDateOrderGroups;
                        newGroup.orderItems = currOrderItems;
                        newoOderItemGroups.Add(newGroup);
                    }
                }
                else {
                    newoOderItemGroups.Add(group);
                }
            }
            this.orderItemGroups = newoOderItemGroups;
        }
        public List<List<NurseOrderItem>> getListOrderItems(List<NurseOrderItem> orderItems) {
            List<List<NurseOrderItem>> orderItemsList = new List<List<NurseOrderItem>>();
            List<NurseOrderItem> newOrderItems = new List<NurseOrderItem>();
            for (int i = 0; i < orderItems.Count; i++) {

                if ((i % 2 == 0) && (i != 0))
                {
                    orderItemsList.Add(newOrderItems);
                    newOrderItems = new List<NurseOrderItem>();
                }
                newOrderItems.Add(orderItems[i]);
                if (i == orderItems.Count - 1) {
                    orderItemsList.Add(newOrderItems);
                }
            }

            return orderItemsList;
        }

```

# 修改方法

doc_PrintPage

在最开始调用这个方法

handleOrderItemGroups(this.orderItemGroups);